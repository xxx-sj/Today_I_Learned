# remove

엔티티를 삭제하려면 먼저 삭제 대상 엔티티를 조회해야한다.

```java
Member memberA = em.find(Member.class, "memberA");
em.remove(memberA);
```
엔티티를 즉시 삭제하는 것이 아니라 엔티티 등록과 비슷하게 삭제 쿼리를 쓰기 지연 SQL 저장소에 등록한다. 이 후 트랜잭션을 커밋해서 플러시를       
호출하면 실제 데이터베이스에 삭제 쿼리를 전달한다. 
em.remove(memberA)를 호출하는 순간 memberA는 영속성 컨텍스트에서 제거된다. 삭제된 엔티니는 재사용하지 않고 가비지 컬렉션의 대상이 되도록 두는것이 좋다.    

##### 출처: 자바 ORM표준 JPA 프로그래밍
