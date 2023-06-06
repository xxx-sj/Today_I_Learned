# 동적 쿼리 - where 다중 파라미터 사용

JpaQueryFactory의 where절에 null이 들어가면 무시를 하는데, 이것을 이용하여 where절의 각각의 condition에 해당하는 method를      
작성하여 동적쿼리를 작성한다.     

```java
private List<Member> searchMember2(String usernameParam, Integer ageParam) {
    return queryFactory
            .selectFrom(member)
            .where(usernameEq(usernameParam), ageEq(ageParam))
            .fetch();
}

private Predicate usernameEq(String usernameParam) {
    if (usernameParam == null) {
        return null;
    }

    return member.username.eq(usernameParam);
}

private Predicate ageEq(Integer ageParam) {
    if (ageParam == null) {
        return null;
    }
    return member.age.eq(ageParam);
}
```

이렇게 메소드를 사용하게 되면 재사용이 증가하여 다른 sql 문에서도 사용이 가능하며 추가로 메소드끼리 나열을 할 수 있게 된다.     
메소드끼리 나열을 하기 위해서는 하위 구현체인 BooleanExpression을 반환타입으로 설정해야 한다.     
where절에 인자로 받는것은 Predicate이지만, QType.eq()나, and() 메서드의 반환타입을 보면 BooleanExpression인 것을 확인 할 수 있다.      
각각 eq() 메서드는 SimpleExpression class에 정의되어있고, and()는 BooleanExpression에 정의되어있다.    

```java
private BooleanExpression bindEq(String usernameParam, Integer ageCond) {
    return usernameEq(usernameParam).and(ageEq(ageCond));
}
```


##### 출처: https://www.inflearn.com/course/lecture?courseSlug=querydsl-%EC%8B%A4%EC%A0%84&unitId=30140&category=questionDetail&tab=curriculum
