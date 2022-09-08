## 서브쿼리 동작방식 이해하기

### FILTER 동작방식

-   Main SQL에서 추출된 데이터 건수만큼 서브쿼리가 반복적으로수행되면 처리되는 방식
-   Main SQL의 추출 결과에 대해서, 매 로우마다 서브쿼리에 조인 연결 값을 제공한 후 수행한 후, TRUE일 경우 데이터를 추출
-   Main SQL의 결과가 100만건이면 최대 100만번 수행
-   이런 이유로 적절한 인덱스가 없을 경우 Full Table Scan을 100만번 수행되어 SQL 성능 저하 발생
-   Main SQL의 추출 결과가 많더라도 서브쿼리의 Input 값이 동일하면 Filter가 1번만 수행되는 Filter Optimization이라 불리는 최적화 작업을 수행하므로 성능 문제가 발생하지 않는다.
-   서브 쿼리의 Input 값이 동일한 확률이 매우 희박하므로 추출 건수가 많은 경우 서브커리를 Filter 동작 방식으로 처리할 경우 성능상 비횰율적인 경우가 더 많음
-   Filter 동작 방식으로 수행될 경우 Input 값의 종류가 적어 성능에 유리한지를 반드시 확인해야 함.

#### Filter 방식 수행

-   FILTER 방식은 Main SQL의 추출 결과가 많고 서브쿼리에 제공해 주는 값(INPUT)의 종류가 많다면 성능이 좋지 않음
-   Main SQL의 추출 건수가 적거나, Main SQL의 추출결과가 많을 경우 INPUT값의 종류가 적으면 성능 양호
-   FILTER 방식으로 수행되면 먼저 서브쿼리의 조인 연결 컬럼에 인덱스가 존재하는지 확인해야 함.  
    FULL TABLE SCAN으로처리된다면 심각한 성능 문제가 발생할 수 있음


### 조인 동작 방식

-   조안방식과 FILTER 동작방식과 비교시 큰 차이점은 가변성이다.
-   FILTER는 수행순서나 수행방법이 고정 (Main SQL -> SubQuery) 다양한 상황에 유연한 대처가 어려움
-   조인 동작 방식은 Nest Loops Join, Hash Join, Sort Merge Join, Semi Join, Anti Join등의 다양한 조인 방법 중 유리한 것을 선택 가능
-   SEMI/ANTI JOIN을 제외하고 수행 순서까지 선택 가능
-   Nested Loops Join Semi를 제외한 나머지 조인 방법은 Filter 동작 방식이 가지고 있는 FILTER 오퍼레이션 의 효과는 없음.
-   INPUT값의 종류가 적은 경우라면, FILTER 방식이 성능상 유리 할 수 있음.




### 서브쿼리를 이용한 SQL 성능개선

### Minus 대신 NOT EXISTS사용

MINUS 문 

select ... From A     MINUS     select ... From B

#### 수행방식
A에서 데이터 추출 -> 추출한 데이터 SORT연산 -> B에서 데이터 추출 -> 추출된 데이터 SORT 연산

==> 두 결과를 비교하면서 최종 데이터 추출


NOT EXISTS

select ... From A    where Not Exists(select ... From B where B.XX = A.XX)

A에서 데이터 추출 -> 추출한 데이터와 서브쿼리 데이터 B데이터와 존재유무 체크 후 최종 데이터 추출

