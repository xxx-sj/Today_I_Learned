# 동적 쿼리와 성능 최적화 조회 Builder

@QueryProjection을 통해 DTO에서 Qfile을 생성한 후, BooleanBuilder를 통해 동적 쿼리를 작성한다.     
if문을 통해 searchDto로 넘어온 값을 검증하며 값을 설정한 후 where절에 전달한다.    

```java
public List<MemberTeamDto> searchByBuilder(MemberSearchCondition condition) {

    BooleanBuilder builder = new BooleanBuilder();
    if (StringUtils.hasText(condition.getUsername())) {
        builder.and(member.username.eq(condition.getUsername()));
    }
    if (StringUtils.hasText(condition.getTeamName())) {
        builder.and(team.name.eq(condition.getTeamName()));
    }
    if (condition.getAgeGoe() != null) {
        builder.and(member.age.goe(condition.getAgeGoe()));
    }
    if (condition.getAgeLoe() != null) {
        builder.and(member.age.goe(condition.getAgeLoe()));
    }


    return queryFactory
            .select(new QMemberTeamDto(
                    member.id.as("memberId"),
                    member.username,
                    member.age,
                    team.id.as("teamId"),
                    team.name.as("teamName")
            ))
            .from(member)
            .leftJoin(member.team, team)
            .where(builder)
            .fetch();
}
```
사용 시 주의해야할 점은 만약, 조건이 모두 없을 경우엔 findAll()과 동작이 같은데, 이 때, 조회될 row수가 많다면 성능에 문제가 발생할 수 있다.     
따라서 이럴 땐 기본적인 조건이 추가되거나, paging 또는 limit이 넣어놓는 것을 권장한다.

##### 출처: https://www.inflearn.com/course/lecture?courseSlug=querydsl-%EC%8B%A4%EC%A0%84&unitId=30145&category=questionDetail&tab=curriculum
