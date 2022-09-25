# 엔티티 그래프

엔티티 조회시점에 연관된 엔티티들을 함께 조회하는 기능
- 정적으로 정의하는 Named 엔티티 그래프
- 동적으로 정의하는 엔티티 그래프

## 정적 엔티티 그래프(Named)
c. 주문을 조회 시 연관된 회원도 함께 조회하는 엔티티 그래프 정의

```Java 
@NamedEntityGraph(
	name = "Order.withMember", attributeNodes = {    
    @NamedAttributeNode("member")
})
    @Entity@Table(name = "ORDERS")
    public class Order {     
    @Id @GeneratedValue @Column(name = "ORDER_ID")    
    private Long id;     
    
    @ManyToOne(fetch = FetchType.LAZY, optional = false)    
    @JoinColumn(name = "MEMBER_ID")    
    private Member member;  // 주문 회원     //..}
```

@NamedEntityGraph : Named 엔티티 그래프 정의
name : 엔티티 그래프 이름 정의
attributeNodes : 함께 조회할 속성 선택

- Order.member가 지연 로딩으로 설정되어 있지만, 엔티티 그래프에서 함께 조회할 속성으로 member를 선택했으므로 이 엔티티 그래프를 사용하면 Order를 조회할 때 연관된 member도 함께 조회 가능

- 엔티티 그래프를 둘 이상 정의할 경우 @NamedEntityGraphs 사용
 
### em.find()에서 엔티티 그래프 사용

```Java
EntityGraph graph = em.getEntityGraph("Order.withMember"); 
Map hints = new HashMap();
hints.put("javax.persistence.fetchgraph", graph); 
Order order = em.find(Order.class, orderId, hints);
```
 - em.getEntityGraph("") : 정의한 엔티티 그래프 찾기
 - hints.put("", ) : 엔티티 그래프는 JPA의 힌트 기능을 사용해서 동작힌트의 값으로 찾아온 엔티티 그래프 사용
 - em.find() : 엔티티 그래프 사용
  
 
 
 
 ### subgraph
 Order -> OrderItem -> Item 조회c. subgraph 정의
 ```Java
 @NamedEntityGraph(name = "Order.withAll", attributeNodes = {    
 @NamedAttributeNode("member"),    
 @NamedAttributeNode(value = "orderItems", subgraph = "orderItems")    },    
 
 subgraphs = @NamedSubgraph(name = "orderItems", attributeNodes = {@NamedAttributeNode("item")})
 )
 
 @Entity@Table(name = "ORDERS")public class Order {     
 @Id    @GeneratedValue    @Column(name = "ORDER_ID")    
 private Long id;     
 
 @ManyToOne(fetch = FetchType.LAZY, optional = false)    
 @JoinColumn(name = "MEMBER_ID")    
 private Member member;  // 주문회원     
 
 @OneToMany(mappedBy = "order", cascade = CascadeType.ALL)    
 private List<OrderItem> orderItems = new ArrayList<OrderItem();    } 
 
 @Entity@Table(name = "ORDER_ITEM")
 public class OrderItem {     
 @Id    @GeneratedValue    @Column(name = "ORDER_ITEM_ID")    
 private Long id;     @ManyToOne(fetch = FetchType.LAZY)    
 
 @JoinColumn(name = "ITEM_ID")    
 private Item item;  
 
 // 주문 상품     // ..}
```
 
 "Order.All"이라는 Named 엔티티 그래프 정의: 
 아래 객체 그래프를 함께 조회
 - Order -> Member
 - Order -> OrderItem
 - OrderItem -> Item  (Order의 객체 그래프가 아니므로 subgraphs 속성에 정의)c. orderItem -> Item 조회
 ```Java
 Map hints = new HashMap();
 hints.put("javax.persistence.fetchgraph", em.getEntityGraph("Order.withAll")); 
 Order order = em.find(Order.class, orderId, hints);```
 #### JPQL에서 엔티티 그래프 사용
 em.find()와 동일하게 힌트만 추가
 ```Java
 List<Order> resultList =em.createQuery("select o from Order o where o.id = :orderId",Order.class).setParameter("orderId", orderId).setHint("javax.persistence.fetchgraph", em.getEntityGraph("Order.withAll")).getResultList();
 ```
 
 ## 동적 엔티티 그래프
 엔티티 그래프를 동적으로 구성하려면 createEntityGraph() 메소드 사용
 
 ### 동적 엔티티 그래프 구성
 ```Java
 EntityGraph<Order> graph = em.createEntityGraph(Order.class);
 graph.addAttributeNodes("member");     
 Map hints = new HashMap();
 hints.put("javax.persistence.fetchgraph", graph); 
 Order order = em.find(Order.class, orderId, hints);
 ```
 ### subgraph
 ```Java
 EntityGraph<Order> graph = em.createEntityGraph(Order.class);
 graph.addAttributeNodes("member");
 Subgraph<OrderItem> orderItems = graph.addSubgraph("orderItems");
 orderItems.addAttributeNodes("item"); 
 Map hints = new HashMap();
 hints.put("javax.persistence.fetchgraph", graph); 
 Order order = em.find(Order.class, orderId, hints);
 ```
