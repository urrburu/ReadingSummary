## Reachable?

Java의 가비지 콜렉션 기능을 작동하면 객체들을 앞으로 사용할 객체와 사용하지 않는 객체로 분류하고 사용하지 않는 객체들을 메모리에서 해제한다. 이 기준이 되는 개념이 Reachable 이다.


## Mark and Sweep Algorithm


## Eden, Young Generation, Old Generation



# GC 알고리즘 종류

## ZGC

-   힙 사이즈가 대형 일수록 효율적
-   `G1GC`와 달리 대형 힙에서의 튜닝이 **필요 없음**
-   `JDK 11`부터 사용 가능, `JDK 15`부터 `production ready` (다만 패치가 백포팅된 몇몇 JDK에서는 JDK 버전이 15 미만이어도 `production ready`)
-   `Oracle`에서 개발
-   `'G1GC보다 빠른 Shenandoah GC'` 보다도 압도적으로 빠름. realtime 애플리케이션에 매우 적합. (1TB 힙에서도 GC pause `1ms` 미만, G1GC는 100ms 이상)  
    (`G1GC` < `Shenandoah GC` < `ZGC`)

~~다만, 한번 점유한 메모리를 해제하지 않음 _(2021/8 패치 이전)_~~

[관련 링크](https://openjdk.java.net/jeps/351)

-   점유한 메모리를 자동으로 해제하는 기능이 추가됐음
-   `-XX:(+/-)ZUncommit`로 확인 가능
-   기본값은 `활성화`

### Q. ZGC를 사용하고 있는데, 메모리 사용량이 Xmx값보다 훨씬 높게 보여요!

A. [https://stackoverflow.com/questions/62926652](%EB%8B%B5%EB%B3%80)  
tl;dr - ZGC는 colored pointers 라는 기술을 사용하기 때문에, 실제 메모리 사용량보다 약 3배 더 큰 메모리 사용량으로 보일 수 있음. (예: `512MB` 힙은 `1.5GB`로 보일 수 있음)

### ZGC하고 CompressedOops를 같이 사용할 수 없어요.

ZGC는 `CompressedOops`를 사용할 수 없기 때문에  
32GB 미만의 힙인 경우, 미미한 성능 저하가 나타날 수 있음

다만, JDK 15부터 (패치가 백포팅 된 JDK도 있음) `-XX:+UseCompressedClassPointers`를 ZGC와 함께 사용할 수 있음  
따라서, 해당 옵션을 활성화할 경우 성능 저하가 나타나지 않음

## Shenandoah GC

-   `JDK 12`부터 사용 가능 (일부 JDK), `JDK 15`부터 `production ready`
-   `redhat`에서 개발
-   `G1GC`와 달리, `튜닝 없이도 저지연 GC를 제공하는 것`을 목표로 함 (`ZGC` 목표와 비슷함)
-   `ZGC`와 달리, `크기가 매우 작은 힙`에서도 잘 작동함
-   `G1GC`와 코드가 비슷한 편, 다만 대부분의 작업이 비동기로 진행됨
-   기본값은 세대 (`Young`/`Old`) 없는 GC. 다만, 2021/9 패치로 세대 있는 GC가 추가됨. (`Zulu JDK 17` 기준, 아직 이용 불가)

![](https://blog.kakaocdn.net/dn/vo2P9/btrg2hK8Mvo/4K2zmq9yKOuZvh0iFbO8O1/img.jpg)

(프로덕션 서버에 `Shenandoah GC`를 적용한 [Grammarly](https://www.grammarly.com/))

-   적용 이후 대부분의 `GC Spike`가 사라진 것을 볼 수 있음
    
-   Grammarly 개발자 왈, `'GC 튜닝을 엄청나게 한 G1GC'` 쓰던 때보다 `'GC 튜닝을 하나도 하지 않은 Shenandoah GC'` 쓸 때 서비스가 훨씬 안정적으로 수행되었으며, 성능 병목 현상도 해소되었다고 한다. (전체적인 QoS가 향상되었다고 함)
    
    ```
    Suddenly, the service started to perform much more reliably.
    Several performance bottlenecks have been lifted, allowing us to push for higher throughput per machine.
    We could raise the heap size to 57g (it actually started with 20g) without the latencies growing much.
    Bigger heap gave a larger buffer to handle traffic spikes.
    The overall QoS has improved, significantly reducing latency percentiles over a longer timespan.
    ```
    

출처: [https://redminit.tistory.com/entry/optimize-jvm-gc](https://redminit.tistory.com/entry/optimize-jvm-gc) [Mini's IT:티스토리]