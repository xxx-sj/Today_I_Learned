# BooleanBuilder

BooleanBuilder를 통해 동적 쿼리를 작성할 수 있다.
BooleanBuilder에서는 생성자에 기본 파라미터값을 설정할 수 있으며 [null이 아니여야 한다는 가정하에] 
if 문을 통해 BooleanBuilder에 값을 설정한다. 이 후 queryFactory의 where절에 값이 들어가게 된다.    

```java
@Test
public void dynamicQuery_BooleanBuilder() {
    String usernameParam = "condition1";
    Integer ageParam = 0;

    List<Member> result = searchCondition(usernameParam, ageParam);

    Assertions.assertThat(result.size()).isEqualTo(1);
}

private List<Member> searchMember1(String usernameCond, Integer ageCond) {

    BooleanBuilder builder = new BooleanBuilder();
    if (usernameCond != null) {
        builder.and(member.username.eq(usernameCond));
    }

    if (ageCond != null) {
        builder.and(member.age.eq(ageCond));
    }

    return queryFactory
            .selectFrom(member)
            .where(builder)
            .fetch();
}
```

##### 출처: https://www.inflearn.com/course/lecture?courseSlug=querydsl-%EC%8B%A4%EC%A0%84&unitId=30139&category=questionDetail&tab=curriculum
