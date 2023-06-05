# 조인 -on절

- On절을 활용한 조인(JPA2.1)
  - join 대상 필터링
  - 연관관계 없는 엔티티 외부 조인


### join 대상 필터링
```java
/**
 * 예) 회원과 팀을 조인하면서, 팀 이름이 teamA인 팀만 조인, 회원은 모두 조회
 * JPQL: select m, t from Member m left join m.team t on t.name = 'teamA'
 */
@Test
public void join_on_filtering() {
    List<Tuple> result = queryFactory
            .select(member, team)
            .from(member)
            .leftJoin(member.team, team)
            .on(team.name.eq("teamA"))
//                .where(team.name.eq("teamA"))
            .fetch();

    for (Tuple tuple : result) {
        System.out.println("tuple = " + tuple);
    }
}
```
lefjoin을 통해 데이터를 가져오면 member의 데이터를 모두 가져오는데 이 때, on절에 join 대상의 table [team] 에서 조건을 통해 join인 할 데이터를 줄일 수 있다.
innterjoin같은 경우는 on절에 쓴 조건이 where절에 쓴것과 동일하기 때문에 우리에게 익숙한 where절에 쓰는것을 추천한다.     


### 연관관계 없는 엔티티 외부 조인

```java
/**
* 연관 관계가 없는 엔티티 외부 조인
* 회원의 이름이 팀 이름과 같은 회원 조회
*/
@Test
public void join_on_no_relation() {
  em.persist(new Member("teamA"));
  em.persist(new Member("teamB"));
  em.persist(new Member("teamC"));

  List<Tuple> result = queryFactory
          .select(member, team)
          .from(member)
          .leftJoin(team).on(member.username.eq(team.name))
          .fetch();

  for (Tuple tuple : result) {
      System.out.println("tuple = " + tuple);
  }
}
```

##### 출처: https://www.inflearn.com/course/lecture?courseSlug=querydsl-%EC%8B%A4%EC%A0%84&unitId=30130&tab=curriculum&category=questionDetail
