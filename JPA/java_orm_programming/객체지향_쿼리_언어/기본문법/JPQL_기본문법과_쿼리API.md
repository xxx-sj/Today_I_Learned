# JPQL 기본문법과 쿼리 api

- select m from Member as m wher m.age > 18
- 엔티티와 속성은 대소문자 구분 (Member,age)
- JPQL 키워드는 대소문자 구분 x(select, from, where)
- 엔티티 이름 사용, 테이블 이름이 아님(x) @Entity(name = "")
- 별칭은 필수(m) as는 생략가능


### TypeQuery, Query
- typequery :  반환 타입이 명확할 때
- query : 반환 타입이 명확하지 않을 때
```java
TypeQuery<Member> m = em.createQuery(~, Member.class);
Query m = em.createQuery(m.username, m.age, Member.class);
```

### 결과조회 API

- 하나 이상일 때 : query.getRequestList() : 결과가없으면 빈 리스트
- querty.getSingleResult(): 결과가 하나일 때, 없으면 noReulstException, 둘 이상이면 NonUniqueException
 
### 파라미터 바인딩

#### 이름기준
```java
TypedQuery<MEmber> query = em.createQuery("select m from Member m where m.username = :username);
query.setParameter("username", "member1");
                   [ name, value ]
 
 query.getSingleResult();
 
//method chain 가능
```

### 위치기반
- 되도록 사용하지 않도록



##### 출처: https://www.inflearn.com/course/lecture?courseSlug=ORM-JPA-Basic&unitId=21719&tab=curriculum
