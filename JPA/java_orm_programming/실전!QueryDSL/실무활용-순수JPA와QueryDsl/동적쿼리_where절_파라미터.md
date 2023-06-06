# 동적쿼리 작성 where절의 파라미터를 통해

where절의 조건을 method로 따로 빼내어 코드의 재사용성을 높이면서 동시에 코드의 간결화를 얻을 수 있다.    
as() 메서드가 필요한 이유는 dto의 property 이름을 맞추기 위해서이다.     

```java
public List<MemberTeamDto> search(MemberSearchCondition condition) {
  return queryFactory
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
          )
          .fetch();
}

private BooleanExpression usernameEq(String username) {
  return StringUtils.hasText(username) ? member.username.eq(username) : null;
}

private BooleanExpression teamNameEq(String teamName) {
  return StringUtils.hasText(teamName) ? team.name.eq(teamName) : null;
}

```
##### 출처: https://www.inflearn.com/course/lecture?courseSlug=querydsl-%EC%8B%A4%EC%A0%84&unitId=30146&category=questionDetail&tab=curriculum
