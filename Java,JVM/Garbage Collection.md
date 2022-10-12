## Reachable?

Java의 가비지 콜렉션 기능을 작동하면 객체들을 앞으로 사용할 객체와 사용하지 않는 객체로 분류하고 사용하지 않는 객체들을 메모리에서 해제한다. 이 기준이 되는 개념이 Reachable 이다.


## Mark and Sweep Algorithm

힙에서 Reachable 객체를 먼저 찍어주고 Reachable하지 않은 객체들의 메모리 영역을 자동으로 해제해준다. 


## Eden, Young Generation, Old Generation
에덴영역 -> 서바이브0영역 -> 서바이브1영역 -> 올드 제네레이션-> 영구영역으로 
계속 사용할 일이 있는 객체들이 넘어가게 된다. 
사용했던 변수를 계속 사용하리라고 예측하고 넘겨주는 것이다. 

-   Eden: 새로 생성한 대부분의 객체가 위치하는 곳
-   S0, S1: Eden 영역에서 GC가 한번 발생한 후 살아남은 객체들이 존재하는 곳
-   Old Memory: Young Generation에 대한 GC가 반복되는 과정속에 살아남은 객체가 살아남는 곳 특정 회수 이상 참조되어 Old 영역으로 가기 위한 Age를 달성하였을 때 이동하게 된다.
-   Perm: Class / Method 의 Meta 정보, static 변수 / 상수들이 저장되는 곳


1.  Young Generation 영역: 대부분의 객체가 GC 되는 영역, 이 영역에서 객체가 사라질 때 **Minor GC** 가 발생
2.  Old Generation 영역: Young 영역보다 크게 할당되지만, GC는 적게 발생. 이때는 **Full GC** 라고 일컫음  
    그렇기 때문에 **Full GC** 는 이 `stop the world` 시간이 길 수 밖에 없습니다  
    기본적으로 메모리가 크고, 처리해야 될 양이 많기 때문이죠  
    이 old 영역에 대한 GC를 다르게 하기 위해서 많은 알고리즘들이 존재하는데요

-   Serial GC: Heap의 앞부분부터 확인하여 살아있는 것만 남기고(Sweep), 객체들이 연속되도록 Compaction 하는 작업
    
    기본적으로 Mark-Sweep-Compaction 알고리즘에 해당  
    Serial GC는 적은 메모리와 CPU 코어 개수가 적을 때 가장 올바름 (싱글 쓰레드)
    
-   Parallel GC: Serial GC의 멀티쓰레드 버젼
-   Parallel Old GC: Parallel GC와 다른점은 `Mark-Summary-Compaction` 단계를 거쳐서 객체를 식별
    
    Summary 에 해당하는 작업이 GC를 수행한 영역에 대해서 살아있는 객체를 식별한다는 작업
    
-   Concurrent Mark & Sweep GC = CMS GC: CMS GC는 다른 GC와는 다르게 `Compaction` 을 진행하지는 않는다
    1.  Initial Mark: 클래스 로더에서 가장 가까운 객체중 살아 있는 객체만 찾는다
    2.  Concurrent Mark: 위에서 살아있다고 확인한 객체에서 참조되고 있는 객체를 확인한다
    3.  Remark: 위 단계에서 새로 추가되거나 참조가 끊긴 객체를 확인한다
    4.  Concurrent Sweep: 쓰레기를 정리한다

# GC 알고리즘 종류

## G1GC

앞서 언급했던 Eden, Survivor, Old 영역이 존재하지만, 해당 영역은 고정된 크기가 아니며  
전체 Heap 메모리 영역을 Region 이라는 특정한 크기로 나눈 것이고  
Region의 상태에 따라 그 Region의 역할(Eden, Survivor, Old)가 동적으로 변동합니다  
Region은 기본적으로 ( 전체 Heap 메모리 ) / 2048 로 default 값이 지정되어 있습니다.

-   Humonogous: Region 크기의 50%를 초과하는 큰 객체를 저장하기 위한 공간
-   Available/Unused: 아직 사용되지 않은 Region

`G1GC` 에서도 마찬가지로 **Minor GC** 가 존재하며, 요 과정에는 살아남은 객체들을 Survivor Region으로 옮기고, Eden에 대한 영역을 사용가능한(Availabe)Region으로 돌리는 형태로 과정이 일어나게 됩니다  
반면 `G1GC` 에는 **Full GC** 와 유사한 **Concurrent Cycle** 이라는 과정이 존재하는데요,  
해당 과정은 `IHOP(InitiatingHeapOccupancyPercent)` 에서 정한 수치를 초과하면 실행하게 됩니다.


1.  **Initial Mark** : Old Region에 존재하는 객체들이 참조하는 Survivor Region을 찾는다(STW)
2.  **Root Region Scan** : 위에서 찾은 Survivor 객체들에 대한 스캔 작업을 실시한다
3.  **Concurrent Mark** : 전체 Heap의 scan 작업을 실시하고, GC 대상 객체가 발견되지 않은 Region은 이후 단계를 제외한다
4.  **Remark** : 애플리케이션을 멈추고(STW) 최종적으로 GC 대상에서 제외할 객체를 식별한다
5.  **Cleanup** : 애플리케이션을 멈추고(STW) 살아있는 객체가 가장 적은 Region에 대한 미사용 객체를 제거한다
6.  **Copy** : GC 대상의 Region이었지만, Cleanup 과정에서 완전히 비워지지 않은 Region의 살아남은 객체들을 새로운 Region(Available/Unused) Region에복사하여 Compaction을 수행한다
7.  살아있는 객체가 아주 적은 Old 영역에 대해 [GC pause(mixed)] 를 로그로 표시하고, Young GC가 이루어질 때 수집되도록 한다

![](https://blog.kakaocdn.net/dn/bWj6yv/btqMqBft2cj/T69AhDZYGwLomksxpLb82k/img.png)


## ZGC(JDK15 이상에서 사용하는 최신 GC)

-   힙 사이즈가 대형 일수록 효율적
-   `G1GC`와 달리 대형 힙에서의 튜닝이 **필요 없음**
-   `JDK 11`부터 사용 가능, `JDK 15`부터 `production ready` (다만 패치가 백포팅된 몇몇 JDK에서는 JDK 버전이 15 미만이어도 `production ready`)
-   `Oracle`에서 개발
-   `'G1GC보다 빠른 Shenandoah GC'` 보다도 압도적으로 빠름. realtime 애플리케이션에 매우 적합. (1TB 힙에서도 GC pause `1ms` 미만, G1GC는 100ms 이상)  
    (`G1GC` < `Shenandoah GC` < `ZGC`)


적은 메모리나 큰 메모리에서 STW 시간을 최대한 적게(10ms 이하로) 가져가기 위해 제작되었다.
STW는 스탑 더 월드, GC를 돌리는 동안 모든 스레드는 중지되어야 하기 때문에.
~~다만, 한번 점유한 메모리를 해제하지 않음 _(2021/8 패치 이전)_~~

[관련 링크](https://openjdk.java.net/jeps/351)

-   점유한 메모리를 자동으로 해제하는 기능이 추가됐음
-   `-XX:(+/-)ZUncommit`로 확인 가능
-   기본값은 `활성화`

### Q. ZGC를 사용하고 있는데, 메모리 사용량이 Xmx값보다 훨씬 높게 보여요!

A. [https://stackoverflow.com/questions/62926652]
 ZGC는 colored pointers 라는 기술을 사용하기 때문에, 실제 메모리 사용량보다 약 3배 더 큰 메모리 사용량으로 보일 수 있음. (예: `512MB` 힙은 `1.5GB`로 보일 수 있음)

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