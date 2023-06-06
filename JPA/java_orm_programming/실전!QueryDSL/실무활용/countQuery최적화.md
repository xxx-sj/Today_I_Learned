# count query 최적화

### count 쿼리 생랼 가능한 경우
- 페이지 시작이면서 [index 0] 컨텐츠 사이즈[content.size()]가 페이지 사이즈보다 작을 때
- 마지막 페이지 일때 (offset + 컨텐츠 사이즈를 더해서 전체 사이즈를 구함) -> 마지막 페이지일 경우 offset의 값 [index] + 컨텐츠 사이즈를 더해서 구함.

**PageableExecutionUtils.getPage(content, pageable, () -> countQuery)**를 사용하여 최적화를 진행한다.

```java
@Override
public Page<MemberTeamDto> searchPageComplex(MemberSearchCondition condition, Pageable pageable) {
    List<MemberTeamDto> content = queryFactory
            .select(
                    new QMemberTeamDto(
                            member.id.as("memberId"),
                            member.username,
                            member.age,
                            team.id.as("teamId"),
                            team.name.as("teamName")
                    ))
            .from(member)
            .leftJoin(member.team, team)
            .where(
                    usernameEq(condition.getUsername()),
                    teamNameEq(condition.getTeamName()),
                    ageGoe(condition.getAgeGoe()),
                    ageLoe(condition.getAgeLoe())
            )
            .offset(pageable.getOffset())
            .limit(pageable.getPageSize())
            .fetch();

    JPAQuery<Long> countQuery = queryFactory
            .select(member.count())
            .from(member)
            .leftJoin(member.team, team)
            .where(
                    usernameEq(condition.getUsername()),
                    teamNameEq(condition.getTeamName()),
                    ageGoe(condition.getAgeGoe()),
                    ageLoe(condition.getAgeLoe())
            );

    return PageableExecutionUtils.getPage(content, pageable, () -> countQuery.fetchOne());
}
```



##### 출처: https://www.inflearn.com/course/lecture?courseSlug=querydsl-%EC%8B%A4%EC%A0%84&unitId=30152&category=questionDetail&tab=curriculum
