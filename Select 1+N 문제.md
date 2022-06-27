# Select 1+N 문제
#### 지연로딩의 경우 하나의 쿼리를 날릴때 관계된 데이터베이스까지 질의문이 함께 나가면서 쿼리가 너무 많이 나가는 문제가 존재했다.

양방향 연관관계가 걸려 있는 경우 예측이 잘 안된다. 

쿼리는 쿼리대로 나가고 성능은 성능대로 나가지 않는 문제가 존재한다. 첫 JPQL은 일단 데이터를 갖고온다. 

->  해결법 : 모든 연관관계는 Lazy로 설정하고 이를 fatchJoin으로 만들어 질의문을 생성하면서 이를 해결할 수 있다. 

jpql을 작성할 때, 
em.createQuery{“ select ~~  from ~~ “
				“join fetch o.member m” +
				“join fetch o.delivery d”, Order.class).getResultList();
}

이런식으로 join fetch 를 사용할 경우, 엔티티의 연관관계를 이용해 원하는 정보만을 조회해 갖고 올 수 있다. 

DTO를 이 jpql 문장에 연관시켜서 문제를 단순화 하는 방법도 있다. 


#### fetch join 이란 무엇인가?

 - 일반 Join
	Fetch Join과 달리 연관 Entity에 Join을 걸어도 실제 쿼리에서 SELECT 하는 Entity는
	오직 JPQL에서 조회하는 주체가 되는 Entity만 조회하여 영속화
	조회의 주체가 되는 Entity만 SELECT 해서 영속화하기 때문에 데이터는 필요하지 않지만 연관 Entity가 검색조건에는 필요한 경우에 주로 사용됨
 - Fetch Join
	조회의 주체가 되는 Entity 이외에 Fetch Join이 걸린 연관 Entity도 함께 SELECT 하여 모두 영속화
	Fetch Join이 걸린 Entity 모두 영속화하기 때문에 FetchType이 Lazy인 Entity를 참조하더라도
	이미 영속성 컨텍스트에 들어있기 때문에 따로 쿼리가 실행되지 않은 채로 N+1문제가 해결됨
	
 - Join의 맹점 -> 다른 테이블을 묶어서 조회할 수 있도록 해주지만 엔티티에 대한 영속성 까지 보장해주지는 못한다.
 - 그렇기 때문에 실제 질의하는 대상 Entity와 Fetch join이 걸려있는 Entity를 포함한 컬럼을 함께 갖고 올 수 있는 Fetch Join이 필요하다.







### 하지만 이 마저도 큰 이슈는 아니라고 함 자주 발생되는 성능저하의 원인이지만 주로 다른 부분에서 문제가 많이 발생함
