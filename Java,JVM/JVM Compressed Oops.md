
## 요약

jvm 기반의 애플리케이션이라면 heap 최대 사이즈를 32GB를 넘기지 말도록 고려하라

테스트해보고 30~31GB 중 적당한 사이즈를 사용하자

## 배경

jvm 기반의 애플리케이션을 다루다 보면 매번 고민하게 되는 문제가 있다. STW.

heap을 많이 잡는다면 성능이 잘 나올 것 같은 느낌이 있지만 그렇지 않다.

full gc 가 돌기 시작한다면 애플리케이션은 먹통이 된다.

독립적으로 구성된 애플리케이션이라면 큰 문제가 안될 수도 있다. (물론 HA구성은 잘해둬야 한다.)

클러스터로 구성되는 컴포넌트들에서는 피곤한 결과를 이끌어 낼 수도 있다. (설정을 잘해두면 피할 수 있긴 하다.)

어떤 노드가 full gc 도는 동안, 클러스터를 구성하는 다른 노드들에서는 해당 노드와 통신을 할 수 없으므로 해당 노드가 클러스터를 이탈한 것으로 판단한다.

한 노드가 클러스터에서 빠졌으니 자기들끼리 이리 복구하고, 저리 복구하는 과정을 거치며 '남은 노드들'에 부하가 걸린다.

full gc가 끝나면 '미안, 나 다시 왔어, 너네들 뭐 때문에 그렇게 바빠?'  하는 상황이 발생할 수 있다.

문제는 잠시 이탈했던 노드가 돌아오기 전에 '남은 노드들'에서도 full gc 가 걸릴 수 있다는 것이다.

heap을 무작정 많이 잡으면 안 되는데, 하필 ~32GB로 잡으라고 가이드하는 것인가.

# OOPS (Ordinary Object Pointer)

java는 포인터가 없다. jvm 은 오브젝트의 레퍼런스를 관리하기 위해 oops(ordinary object pointers)라고 불리는 자료구조를 가진다. (자세한 내용은 공부가 더 필요하다. 키워드: instanceOops, Object Memory Layout)

32bit 시스템(ILP32)에서 oops 는 최대 힙을 4GB (2^32)까지 사용할 수 있다.

64bit 시스템(LP64)에서 oops 는 최대 힙을 18.5EB (2^64)까지 사용할 수 있다.

다만 64bit 포인터로 공간을 관리하는 것은 매우 비효율 적이다.

(기존까지는 힙을 많이 잡으면 STW 시간이 증가한다. 그 정도로 큰 메모리는 서버에 안 꽂혀있다.)

## Compressed OOPS

java에서는 트리키 한 방법을 사용했다.

Compressed OOPS를 통해 32bit 만으로도 32GB 힙을 사용할 수 있도록

기존에는 oops가 4GB (2^32) 만큼 메모리 영역을 직접 관리했다.

Compressed OOPS에서는 oops 가 object를 referencing 하는 대신  object의 offset을 referencing 한다.

3bit만큼을 더 사용할 수 있으므로 64bit 레퍼런스가 아닐 때에도 2^32 (4GB) * 2^3 (8) = 32GB만큼의 힙을 사용할 수 있다.

원문 (**This means that we can use up to 32 GB –  2^32+3=2^35=32 GB – of heap space without using 64-bit references.**)

다만, 32GB를 넘게 되면 기존의 oops를 사용하게 된다. (64bit 시스템)

확인은 간단하다

Xmx 를 31G로 설정한 경우, UseCompressedOops 옵션이 켜져 있다.

$ java -Xmx31G -XX:+UnlockDiagnosticVMOptions -XX:+PrintFlagsFinal -version 2>/dev/null | grep 'UseCompressedOops'  
     bool UseCompressedOops                        = true

                                 {lp64_product} {ergonomic}

Xmx 를 32G로 설정한 경우, UseCompressedOops 옵션이 꺼져있다.

$ java -Xmx32G -XX:+UnlockDiagnosticVMOptions -XX:+PrintFlagsFinal -version 2>/dev/null | grep 'UseCompressedOops'  
     bool UseCompressedOops                        = false

                                {lp64_product} {default}

## Zero-based Compressed OOPS

그 외에 다른 분의 블로그를 보다 재미있는 사항을 확인하였다.

compress oops 가 활성화되어있다고 해도 zero based 가 아니면 성능상 손해를 본다는 내용이었다.

jvm 위키에 포함되어 있는 내용을 완벽하게 이해한 것은 아니지만,

zero based oops 가 아닌 경우에는 base 주소로부터 실제 오브젝트의 주소를 찾기 위해 더하기 연산이 필요하지만, zero based oops 일 때에는 더하기 연산이 필요하지 않아 쉬프트 연산만으로 오브젝트의 레퍼런스를 구할 수 있다는 내용이다.

확인했던 방법만 첨부한다.

30G일때에는 Compressed Oops mode: Zero based라는 문구를 확인했지만 31G일 때에는 그렇지 않다.

compressed oops 가 활성화되지 않는 32G 부터는 메시지가 뜨지 않는다.

$ java -Xmx30G -XX:+UnlockDiagnosticVMOptions -Xlog:gc+heap+coops=debug -version  
[0.009s][info][gc,heap,coops] Heap address: 0x0000000080000000, size: 30720 MB, Compressed Oops mode: Zero based, Oop shift amount: 3

$ java -Xmx31G -XX:+UnlockDiagnosticVMOptions  -Xlog:gc+heap+coops=debug -version  
[0.003s][debug][gc,heap,coops] Protected page at the reserved heap base: 0x0000001000000000 / 8388608 bytes  
[0.010s][info ][gc,heap,coops] Heap address: 0x0000001000800000, size: 31744 MB, Compressed Oops mode: Non-zero disjoint base: 0x0000001000000000, Oop shift amount: 3

## 나가는 말

32GB를 최대로 하라고 하지만 그것보다 더 적게 잡아야 할 것 같다.

메모리를 한정적으로 사용할 수 있으므로 한 서버에 2대 노드를 올리는 구성도 가능하다. (고려사항이 있다. 무작정 쓰면 안 된다.)

java의 Object Memory Layout 을 완벽하게 이해하지 못한 채로 전개하다 보니 납득이 잘 안 가는 부분이 있다.


[https://www.baeldung.com/jvm-compressed-oops]