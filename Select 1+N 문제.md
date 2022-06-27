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