# 기본 문법과 쿼리 API

### TypeQuery, Query
- TypeQUery: 반환 타입이 명확할 때 사용 createQuery("selec m from Member as m", Member.class)
- Query 반환타입이 명확하지 않을 때 사용 createQuery("select m.username, m.age from Member m")


### 결과조회 APi
- query.getResulList(): 결과가 하나 이상일 때, 리스트 반환
  - 결과가 없으면 빈 리스트 반환
- query.getSingleResult();: 결과가 정확히 하나, 단일 객체 반환
  - 결과가 없으면 NoResultException
  - 둘 이상이면 NoIUniqueReusltException

### 파라미터 바인딩
```java
em.createQuery("select m from Member m where m.username = :username", Member.class)
.setParameter("username", "member)
.getSingleResult();
```

##### 출처: https://www.inflearn.com/course/lecture?courseSlug=ORM-JPA-Basic&unitId=21719&tab=curriculum&category=questionDetail
