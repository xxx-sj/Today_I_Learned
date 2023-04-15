# JPQL 타입 표현과 기타식

- 문자: 'HELLO', 'She""s'
- 숫자: 10L, 10D, 10F
- Boolean: TRUE, FALSE
- ENUM: jpabook.MemberType.Admin [패키지 모두 포함]

```java

//query
'select m.username from member m where m.type = jpql.memberType.ADMIN';

//parameter
'select m.username from member m where m.type = :userType';
setParameter("userType", MemberType.ADMIN)
```

- 엔티티 타입: TYPE(m) = Member(상속 관계에서 사용) //DTYPE 타입 캐스팅

```java
"select i from Item i where type(i) = BOOK"
```

출처: https://www.inflearn.com/course/lecture?courseSlug=ORM-JPA-Basic&unitId=21724&tab=curriculum
