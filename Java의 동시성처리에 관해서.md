# 자바의 동시성 처리

## 동시성을 고려하지 않는 상태








### synchronized

synchronized는 멀티 스레드 환경에서 동시성 제어를 위해 공유 객체를 동기화하는 키워드이다. synchronized 블록 안에서 관리되는 자원들은 원자성을 보장할 수 있다.

### atomic

atomic 또한 멀티 스레드 환경에서 원자성을 보장하기 위해 나온 개념이다. 동기화(blockin)가 아닌 CAS(Compared And Swap)라는 알고리즘으로 작동하여 원자성을 보장한다.

CAS 알고리즘이란 volatile에서 설명했던 CPU Cache Memory와 RAM을 비교하여 일치한다면 CPU Cache Memory와 RAM에 적용하고, 일치하지 않는다면 재시도함으로써 어떠한 스레드에서 공유 자원에 읽기/쓰기 작업을 하더라도 원자성을 보장한다.

대표적인 예로, 자바의 Concurrent 패키지의 타입들은 CAS 알고리즘을 이용해 원자성을 보장한다.