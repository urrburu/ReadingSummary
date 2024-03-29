## 1. Redis 의 이해

### 1.1 Redis 란 무엇인가?

대규모 서비스를 운영하기 위해선 데이터를 안전하게 그리고 빠르게 저장하고 불러오는 기술이 필요하다.

이런 기술을 위해 Redis 가 존재하고 누군가는 Redis 를 캐싱 솔루션이라고 부르기도 하고 누군가는 NoSQL 의 Key-Value 저장소라고 부르기도 한다.

공통적인 특징은 둘 다 속도가 빠르고 사용하기 쉽다는 것이다.

### 1.2 Redis 의 주요 특성

Redis 의 주요 특징은 다음과 같다.

#### Redis 의 주요 특징

Key-Value 스토어 - Key-Value 를 저장할 수 있는 스토리지를 지원한다.

컬렉션 지원 - List, Set, Sorted Set, Hash 등의 자료구조를 지원한다.

Pub/Sub 지원 - Publish/Subscribe 모델을 지원한다.

디스크 저장(Persistent Layer) - 현재 메모리의 상태를 디스크로 저장할 수 있는 기능과 현재까지의 업데이트 내용을 로그로 저장할 수 있는 AOF 기능이 있다.

복제(Replication) - 다른 노드에서 해당 내용을 복제할 수 있는 Master/Slave 구조를 지원한다.

빠른 속도 - 이상의 기능을 지원하면서도 초당 100,000 QPS(Queries Per Second) 수준의 높은 성능을 지원한다.

-   Key-Value 스토어
    
    -   기본적으로 Redis 는 Key-Value 스토어로 빠르고 간단하게 데이터를 가지고 올 수 있다.
-   컬렉션 지원
    
    -   Redis 는 여러가지 자료구조를 지원하므로 서비스에 맞는 설계를 할 때 유용하게 사용될 수 있다.
-   디스크 저장
    
    -   Redis 의 특징 중 하나는 현재 메모리의 상태를 디스크에 스냅샷 형태로 남길 수 있는 RDB 방식이 있다. 그러므로 Redis 를 restart 할 때 스냅샷에 있는 내용을 가지고 쉽게 복구할 수 있다. 그리고 AOF(Append Only File) 형태로 Redis 에 변경 내역들을 모두 남기는게 가능해서 Write Behind 같은 기능을 지원하는 것도 가능하다.
-   복제
    
    -   Redis 는 Master/Slave 구조로 이용할 수 있어서 Master 의 읽기 부하를 Slave 로 나누는게 가능하다.
-   빠른 성능
    
    -   Redis 를 이용하는 가장 큰 이유는 성능이다. 초당 50,000 ~ 60,000 이상의 처리 속도가 필요하다면 어쩔 수 없이 Redis 나 Memcached 를 이용해야한다.

### 1.3 Redis 와 Memcached 비교

주로 Redis 와 Memcached 를 비교하는 질문이 많은데 아주 단순하게만 비교하자면 Memcached 는 캐싱 솔루션이고 Redis 는 저장소의 개념까지 추가된 것이다.

캐싱이란 말은 빠른 응답을 위해 결과를 저장해주는 솔루션을 말하고 언제든지 사라져도 상관없다. 하지만 저장소의 개념은 데이터가 보존되어야 한다는 말을 말한다. 그래서 Redis 는 RDB 나 AOF 같은 기능을 지원하기도 한다.

그리고 Redis 는 Memcached 에 없는 여러가지 자료구조를 지원해서 개발자의 생산성을 높여주는 일도 해준다.

이외에 Redis 와 Memcached 를 비교하는 표는 다음과 같다.

#### Redis 와 Memcached 의 장단점 비교

기능

DB	                  		Redis                                                  Memcached

- 속도      초당 100,000 QPS 이상                                    초당 100,000 QPS 이상

- 자료구조   List, Set, Sorted Set, Hash 지원                   Key-Value 만 지원

- 안정성     특성을 잘못 이해할 경우에 장애가 발생할 수 있다.      장애가 거의 없다.

- 응답 속도의 균일성  Memcached 에 비해서 응답속도의 균일성이 떨어질 수 있다.    전체적으로 응답속도는 균일하다.

응답 속도의 균일성 같은 경우가 Redis 와 Memcached 가 차이나는 이유는 메모리 할당 구조가 다르기 떄문인데 Redis 같은 경우는 Jemalloc() 을 사용해 메모리 할당을 하고 free() 를 통해서 메모리 할당을 지운다. 반면에 Memcached 는 slab 을 통해서 일정한 사이즈의 메모리를 균일하게 1MB 의 페이지로 자르고 그 안에 또 작은 사이즈 부터 큰 사이즈의 chunk() 를 일정하게 놔두는 식을 이용해 메모리 내부 단편화 현상은 있지만 외부 단편화 현상을 없도록 해서 메모리 관리를 조금 더 효율적으로 이용할 수 있어서 응답 속도가 비교적 균일하다

---

## 2. Redis 운영과 관리

### 2.1 Redis 운영과 관리의 핵심: Redis 는 싱글 스레드다

Redis 를 사용하면 의도하지 않게 성능이 안나오거나 장애가 발생하는 경우가 많은데 Redis 가 싱글 스레드 라는 걸 잊어버려서 생기는 일이 많다.

Redis 는 싱글 스레드 기반이므로 하나의 명령이 오래걸린다면 이는 적합하지 않다.

일단 실무에서 흔히 사용하는 실수의 사례를 살펴보자.

-   서버에서는 Keys 명령을 사용하지 말자.
    
    -   이 명령어는 서버에 저장된 모든 Key 목을 볼 수 있다. 이 명령을 사용하면 모든 Key 를 검색해서 사용하기 때문에 데이터가 적다면 별 문제가 없겠지만 몇백만 개, 몇천만 개가 넘어가면 많은 시간을 소모하게 된다.
-   flushall/flushdb 명령을 주의하자.
    
    -   Redis 에서는 모든 데이터를 삭제하는 flushall/flushdb 명령이 있다. 그리고 Redis 는 db 라는 가상의 공간을 분리할 수 있는 개념을 제공해주고 select 명령으로 이런 db 로 이동할 수 있다. 이를 통해 같은 Key 값이라도 db 가 다르면 다른 value 가 나오게 할 수 있다. 이런 db 하나의 내용을 모두 지우는게 flushdb 이고 모든 db 의 내용을 모두 지울 수 있는게 flushall 이다.
        
    -   Memcached 의 flushall 과는 다르게 Redis 의 flushall 은 지우는데 keys 명령어와 같이 많은 시간이 걸린다. Memcached 같은 경우는 flushall 명령을 하면 Redis 처럼 모든 데이터를 지우는게 아니라 해당 명령이 실행된 시간을 기록하고 이전에 저장된 결과를 조회할려고 하면 없다고 결과를 내면서 그때 지운다. 하지만 Redis 는 실제로 존재하는 모든 데이터를 지우므로 O(n) 이 걸리고 싱글 스레드이기 때문에 많은 시간이 걸린다.
        

### 2.2  Redis Persistent

Redis 와 Memcached 를 구분하는 특성 중에 하나는 Redis 의 데이터를 디스크로 저장할 수 있는 Persistent 기능을 제공한다는 것이다.

Memcached 의 경우 서버가 장애를 일으켜서 restart 하면 모든 데이터가 사라지지만 Redis 같은 경우는 디스크에 저장된 데이터를 기반으로 다시 복구하는게 가능하다.

이런 Persistent 기능은 데이터 스토어로서의 장점이 있지만 이 기능 때문에 장애의 주 원인이 되기도 한다. 그러므로 이 기능을 잘 알아보고 사용하는게 중요하다.

먼저 Redis 에서 제공하는 RDB, AOF 등의 기능에 대해 알아보고 여기서 발생할 수 있는 장애와 이를 피하는 방법에 대해서 알아보자.

#### 2.2.1 RDB

RDB 는 RDBMS 라는 오해를 많이 받지만 RDB 는 단순히 메모리의 스냅샷을 파일 형태로 저장할 때 쓰는 파일의 확장자명이다.

RDB 를 사용해 현재 메모리에 대한 스냅샷을 뜬다면 Redis 에서는 싱글 스레드 기반이므로 많은 시간이 걸릴 수 있겠다고 생각할 수 있다. RDB 를 하는 방식은 두 가지가 있고 SAVE 방식은 이처럼 모든 작업을 멈추고 현재 메모리 상태에 대한 RDB 파일을 생성한다. 실제로 SAVE 옵션으로 50GB 의 메모리 상태를 저장한다면 7 ~ 8분 정도 소요된다고 한다. BGSAVE 는 백그라운드 SAVE 라는 의미로 fork() 명령을 통해서 부모 프로세스로부터 자식 프로세스를 생성하고 그러므로 현재 가지고 있는 메모리의 상태가 복제된 상태에서 데이터를 저장하도록 한다.

RDB 를 사용하려면 Redis 설정 파일인 `redis.conf` 파일에 다음과 같은 내용을 추가해야한다.

```null
# dump.rdb 대신에 다른 파일 이름을 사용해도 괜찮다. 
dbfilename dump.rdb 
```

기본적으로 Redis 애서는 RDB 사용 옵션이 설정되어 있고 save 명령에 따라서 실행이 가능하다.

실행되는 명령은 `save <Seconds> <Changes>` 이런 구조로 사용하고 `save 900 10` 이렇게 사용된다. 이 말은 900초 안에 10번의 변경이 있을 때 RDB 를 사용해 디스크에 스냅샷을 저장한다는 뜻이다.

#### 2.2.2 AOF

AOF 는 "Append Only File" 의 약어로 데이터를 저장하기 전에 AOF 파일에 현재 수행할 명령어를 저장해놓고 장애가 발생하면 AOF 를 기반으로 복구한다.

즉 다음과 같은 순서로 데이터가 저장된다.

-   클라이언트가 Redis 에 업데이트 관련 명령을 요청한다.
    
-   Redis 는 해당 명령을 AOF 에 저장한다.
    
-   파일쓰기가 완료되면 실제로 해당 명령을 수행해서 Redis 메모리에 내용을 변경한다.
    

AOF 는 Redis 설정 파일인 `redis.conf` 에 기본적으로 사용되지 않으므로 AOF 를 사용하려면 다음과 같이 변경해야한다.

```null
# appendonly 는 기본적으로 no 로 설정되어 있다. 
appendonly yes
appendfilename appendonly.aof
appenfdsync everysec
```

-   주의해야 할 설정은 appendfsync 값이다. AOF 는 파일에 저장할 때 파일을 버퍼 캐시에 저장하고 적절한 시점에 이 데이터를 디스크로 저장하는데 appendfsync 는 디스크와 동기화를 얼마나 자주 할 것인지에 대해 설정하는 값으로 다음과 같이 3가지가 있다.
    
-   always: AOF 값을 추가할 때마다 fsync 를 호출해서 디스크에 실제 쓰기를 한다.
    
-   everysec: 매초마다 fsync 를 호출해서 디스크에 실제 쓰기를 한다.
    
-   no: OS 가 실제 sync 를 할 때까지 따로 설정하지 않는다.
    

#### 2.2.3 AOF 와 RDB 의 우선순위

AOF 와 RDB 모두 Persistent 를 구현하는 방법 중 하나인데 두 개의 파일이 모두 있다면 어느 파일을 읽을까?

RDB 는 주기적으로 스냅샷을 띄므로 최신의 데이터를 가지진 못하지만 AOF 는 메모리에 수정사항을 반영하기 전에 항상 디스크에 추가하기 떄문에 둘 다 있다면 AOF 를 기준으로 읽는다.

#### 2.2.4 Redis 가 메모리를 두 배로 사용하는 문제

Redis 가 운영되는 중에 장애를 일으키는 가장 큰 원인은 RDB 를 저장하는 Persistent 기능으로 BGSAVE 방식에서 fork() 를 사용하기 떄문이다.

현대 운영체제에서 fork() 를 사용해서 자식 프로세스를 생성하면 COW(Copy On Write) 라는 기술을 이용해서 부모 프로세스의 메모리에서 실제로 변경이 필요한 부분을 복사한다. 그러므로 해당 부분이 두 개가 존재하게 되므로 메모리를 두 배로 사용하는 경우가 발생할 수 있다.

즉 Redis 에 write 가 발생할 때 마다 그 부분에 해당하는 메모리는 두 배가 될 수 있다. 이로인해 메모리가 부족해서 문제가 생길 수 있다.

Redis 를 사용한다면 실제 Physical 메모리를 모두 사용하지 말고 약간의 여분을 두고 RDB 를 사용한다면 이 경우도 생각해서 메모리의 여분을 남겨두는게 좋다.

#### 2.2.5 Redis 장애: Read 는 가능한데 Write 가 실패하는 경우

Redis 를 운영하면서 RDB 를 사용하는 경우라면 거의 발생하는 장애가 있다. Redis 서버는 동작하지 않는데, 정기적인 Heartbeat 체크는 이상이 없는 경우다.

이러한 문제는 RDB 저장이 실패할 때 기본 설정상 Write 명령이 동작하지 않기 때문에 발생한다.

Redis 는 RDB 저장이 실패하면 해당 장비가 이상이 있다고 생각해서 Write 명령을 더는 처리하지 않고 데이터가 변경되지 않도록 관리한다.

그렇다면 어떠한 이유로 RDB 생성에 실패할까?

-   RDB 를 저장할 수 없을 정도로 디스크에 공간이 없는 경우
    
-   실제 디스크가 고장난 경우
    
-   메모리 부족으로 자식 프로세스를 생성하지 못하는 경우
    
-   누군가 강제적으로 자식 프로세스를 종료시킨 경우
    

위의 이유 등으로 RDB 저장을 실패하면 Redis 내부의 `lastbgsave_status` 라는 변수가 REDIS_ERR 로 설정된다. 그리고 Write 관련 요청은 모두 무시하게 된다.

그렇다면 이 문제는 어떻게 해결해야 할까?

먼저 해당 상황이 맞는지 확인해야 한다. 그 상황을 체크하기 위해선 두 가지 방법이 있다.

첫번째론 Write 명령을 날려보는 방법이 있다.

```null
redis 127.0.0.1:6379 > set a 123
(error) MISCONF Redis is configured to save RDB snapshots. but is currently not able to persist on disk. 
```

두 번째 방법은 info 명령을 이용해 다음 값을 체크하는 것이다.

```null
rdb_last_bgsave_status: ok 
```

---

## 3. Redis 복제

Redis 의 주요 특징 중 하나는 DBMS 가 제공하는 것과 같이 복제(Replication) 기능을 제공하는 것이다.

복제 기능은 장애 발생 시 빠른 서버 교체를 하거나 장비 교체를 할 수 있다. 복제 자체에 대해서는 아는 사람이 많을 것이라 생각하지만 Redis 의 복제가 일반적인 DBMS 의 복제와 어떻게 다른지 유의해서 살펴보면 좋다.

### 3.1 Redis 복제 모델

Redis 는 Master/Slave 형태의 복제 모델을 지원한다. 이를 통해서 Master 의 변경은 Slave 를 타고 전파된다. 그리고 Slave 는 오로지 한 대의 Master 만 가질 수 있다는 것을 기억해두자.

그리고 Slave 가 다른 장비의 Master 가 될 수도 있다.

운영 중인 장비를 슬레이브로 바꾸는 방법은 다음과 같다.

#### 1. `slaveof` 명령을 통해 현재 Redis 를 다른 Redis 의 Slave 로 변경하기

다음과 같이 두 대의 Redis 를 실행해보자.

```null
$ ./redis-server -port 6379
$ ./redis-server -port 6380 
```

6380 포트를 가진 Redis 를 6378 포트 Redis 의 Slave 로 변경해보자.

```null
$./redis-cli -p 6380
redis 127.0.0.1:6380> slaveof 127.0.0.1 6379
OK
```

#### 2. `redis.conf` 를 통해서 현재 Redis 를 다른 Redis 의 Slave 로 변경하기

Redis 설정 파일인 `redis.conf` 를 보면 `slaveof` 이라는 항목이 있다. 여기에 누구의 Slave 로 될건지 명시해주면 된다.

```null
# redis.conf 파일에 다음과 같이 입력하자.
slaveof 127.0.0.1 6378
```

### 3.2 Redis 복제 과정

Redis 의 복제 과정을 간략하게 정리하면 다음과 같다.

1.  Slave 에서 `slaveof` 명령을 이용해서 Master 를 설정한다.
    
2.  Master 가 설정되면 replicationCron 에서 현재 상태에 따라 connectWithMaster 를 호출한다.
    
3.  Master 는 복제를 위해 RDB 를 생성한 후 Slave 에게 전송한다.
    
4.  Slave 는 RDB 를 로드하고 나머지 차이에 대한 명령을 Master 에게 전달받아서 복제를 완료한다.
    

### 3.3 Redis 복제 사용 시 주의 사항

#### 3.3.1 slaveof no one 을 기억하자.

`slaveof no one` 이 명령은 현재 Slave 를 Master 로 승격시킬 때 사용한다.

Redis 의 복제에서 주의할 점은 Slave 와 Master 의 연결이 끊어지고 다시 연결되면 Master 의 최종 결과로 sync 한다는 점인데 Master 에서 장애가 나서 데이터가 하나도 없다면 Slave 도 내용이 모두 사라지는 문제가 발생할 수 있다.

그래서 `slaveof no one` 명령을 통해서 Slave 를 Master 로 승격시키는게 중요하다. 이를 통해서 Slave 는 데이터를 계속해서 유지시키고 업데이트 시켜 나갈 수 있기 때문이다.

#### 3.3.2 복제를 쓰는 경우에는 무조건 백그라운드로 RDB를 생성한다는 점을 주의하자.

일반적으로 RDB 를 사용하면 메모리를 두배로 사용할 가능성이 있기 때문에 RDB 를 사용하지 않는 경우도 있다.

반면 복제를 한다면 사용자의 설정과 무관하게 무조건 Slave 로 전달할 RDB 를 만들어야 하기 때문에 fork 를 해서 RDB 를 백그라운드로 생성한다.

즉 이러한 과정에서 장애가 밣생할 경우가 많기 때문에 하나의 프로세스에서 메모리를 많이 사용하지 않도록 나누는 과정이 필요하다.

### 3.4 Redis 복제를 이용한 실시간 마이그레이션

Redis 서비스를 운영하다 보면 현재의 데이터를 더 좋은 장비로 이동해야 하는 경우가 생긴다.

가능한 다운 타임 없이 바로 데이터를 옮기고 싶어하는 경우가 많은데 이때 Redis 의 복제 기능을 이용하면 다운 타임 없이 쉽게 데이터를 이전하는게 가능하다.

기존 Master 장비를 `127.0.0.1:6379` 라 하고 새로운 장비를 `127.0.0.1:6380` 이라고 하자.

다음과 같은 순서로 작업하면 된자.

1.  데이터 이전을 위해 새로운 장비를 Redis 인스턴스로 실행시킨다.
    
2.  새로운 장비를 기존 장비의 Slave 로 등록 시킨다. 이를 통해 Master 와 Slave 데이터를 동기화 시킨다.
    
3.  새로운 장비의 옵션에 `slave-read-only` 옵션을 끈다. 이를 통해 새로운 장비의 데이터를 계속해서 업데이트 시킨다. 다만 이 설정을 끈다고 Slave 의 변경이  
    Master 로 옮겨가진 않는다.
    
4.  기존의 클라이언트들이 새로운 장비를 Master 로 인식하게끔 설정을 바꾼다.
    
5.  `slaveof no one` 명령을 통해서 새로운 장비를 이제 Master 로 승격시키고 기존 Master 를 종료시킨다.
    


# 내 평가
흠.....그 정돈가.....

일단 실시간 데이터를 빠르게 처리한다는 점은 좋음. 
하지만 수백만건 데이터에 대한 수백건~ 수천건의 캐쉬로 쓰는게 나을듯? 
일단 용량이 적어....... 램의 가격이 비싸니까 사실 진짜 DB가 아니라 DB에 대한 캐쉬고 잦은 변경사항을 실제 DB에 천천히 전파하는 방식으로 

모든 데이터에 대한 답은 될 수 없을 듯. 

그러니까 이 캐시가 정상적으로 작동하려면 전제가 필요한데, 캐시히트가 높아야함......
만약 1달에 한번 평가를 진행하고 그게 한번에 나오는 방식에서는 어울리지 않는 처리방식
게임랭킹처럼 이기면 MMR이 몇점 가산되고 감산되는 형식에서는 충분히 가능

3000명에 대한 랭킹이라고 치면 대략 5000명 정도의 캐쉬만을 저장하고 실시간 변경을 저장하는 방식이 맞지..... 100만명 회원이 있어서 그런 걸 진행할려면...... 레디스로 샤딩을 하면 불가능한건 아니겠지만 비용이 장난아니게 들텐데 메모리가 비싸다......

이런 기술도 좋지만 그냥 조인이나 서브쿼리 쿼리튜닝 기술이 더 중요할거 같다.
왜 Why? -> 테이블이 하나만 있을 경우는 흔치 않다..... 하나의 

보다보니 레디스에 큰 결점이 존재한다. 싱글 스레드라는거.... 그래서 O(N) 연산에 부담이 존재한다. 보통 DB로 이해해보자면 풀테이블 스캔이 해당될 것이다.  그 중에서 키를 모두 긁어오는 작업만 해도 부담이 된다고 한다. 이건 일반 DB에서는 단순 셀렉트 전부 형태라면 부담이 전혀 되지 않는데, 

그래서 부분범위 처리 유도를 더 적극적으로 해줘야 할 것 같다. 