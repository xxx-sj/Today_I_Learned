# querydsl 지원 클래스 직접 만들기

- 장점
- 스프링 데이터가 제공하는 페이징을 편리하게 변환
- ㅈ페이징과 카운트 쿼리 분리 가능
- 스프링 데이터 Sort 지원
- select(), selectFrom()으로 시작가능
- entityManager, queryFactory 제공


직접 만든 클래스에서는 select부터 시작할 수 있다. 

```java
public List<Member> basicSelect() {
    return select(member)
            .from(member)
            .fetch();

}

public List<Member> basicSelectFrom() {
    return selectFrom(member)
            .fetch();
}
```
### pagination
```java
public Page<Member> applyPagination2(MemberSearchCondition condition, Pageable pageable) {
    Page<Member> result = applyPagination(pageable, contentQuery ->
                    contentQuery.selectFrom(member)
                    .leftJoin(member.team, team)
                    .where(usernameEq(condition.getUsername()),
                            teamNameEq(condition.getTeamName()),
                            ageGoe(condition.getAgeGoe()),
                            ageLoe(condition.getAgeLoe())
                    ),
            countQuery -> countQuery
                .select(member.count())
                .from(member)
                .leftJoin(member.team, team)
                .where(usernameEq(condition.getUsername()),
                        teamNameEq(condition.getTeamName()),
                        ageGoe(condition.getAgeGoe()),
                        ageLoe(condition.getAgeLoe()))

    );

    return result;
}
```

##### 출처: https://www.inflearn.com/course/lecture?courseSlug=querydsl-%EC%8B%A4%EC%A0%84&unitId=30158&tab=curriculum
