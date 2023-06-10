# 리포지토리 지원 - QuerydslRepositorySupport


querydslRepositorySupport를 사용하게 되면 이 전에 repo에서 JPAQueryFactory와 EntityManager를 injection 받던 코드를 지울 수 있다.     
추상클래스인 support클래스에서는 생성자에 Class를 인자로 받는데 해당 코드로 이 전의 코드는 없앨 수 있다.

```java
public class MemberRepositoryImpl extends QuerydslRepositorySupport implements MemberRepositoryCustom {

//    private final JPAQueryFactory queryFactory;

//    public MemberRepositoryImpl(EntityManager em) {
//        this.queryFactory = new JPAQueryFactory(em);
//    }
```
이 전에 injection 받던 코드는 
```java
public class MemberRepositoryImpl extends QuerydslRepositorySupport implements MemberRepositoryCustom {
    /**
     * Creates a new {@link QuerydslRepositorySupport} instance for the given domain type.
     *
     * @param domainClass must not be {@literal null}.
     */
    public MemberRepositoryImpl(Class<?> domainClass) {
        super(Member.class);
    }

```
와 같이 사용하게 된다.   

querydsl 3버전에 있던 class여서 from부터 시작하게 되며 entityManager나, JPQQueryFactory를 대신 주입받아 주기 때문에 따로 선언하지 않아도 된다.
```java

@Override
public List<MemberTeamDto> search(MemberSearchCondition condition) {
//support를 이용
    List<MemberTeamDto> result = from(member)
            .leftJoin(member.team, team)
            .where(
                    usernameEq(condition.getUsername()),
                    teamNameEq(condition.getTeamName()),
                    ageGoe(condition.getAgeGoe()),
                    ageLoe(condition.getAgeLoe())
            )
            .select(
                    new QMemberTeamDto(
                            member.id.as("memberId"),
                            member.username,
                            member.age,
                            team.id.as("teamId"),
                            team.name.as("teamName")
                    ))
            .fetch();

//이 전 queryfactory 코드
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
                    ageGoe(condition.getAgeGoe()),
                    ageLoe(condition.getAgeLoe())
            )
            .fetch();
}
```

또한 페이징을 편리하게 할 수 있도록 도와주는데, support 내부에 querydsl이라는 유틸리티 클래스를 통해 작성하게 되며, offset과limit을      
내부에서 설정해준다.    

```java
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
```
위와 같이 작성하던 코드를 
```java
JPQLQuery<MemberTeamDto> jpaQuery = from(member)
        .leftJoin(member.team, team)
        .where(
                usernameEq(condition.getUsername()),
                teamNameEq(condition.getTeamName()),
                ageGoe(condition.getAgeGoe()),
                ageLoe(condition.getAgeLoe())
        )
        .select(
                new QMemberTeamDto(
                        member.id.as("memberId"),
                        member.username,
                        member.age,
                        team.id.as("teamId"),
                        team.name.as("teamName")
                ));

JPQLQuery<MemberTeamDto> query = getQuerydsl().applyPagination(pageable, jpaQuery);

List<MemberTeamDto> fetch = query.fetch();
```
와 같이 작성할 수 있다.      
기존에 select부터 시작하던 코드는 from부터 시작하며, fetch()를 호출하지 않으면 JPQLQuery가 반환되게된다.     
JPQLQuery와 pageable를 getQuerydsl().applyPagination의 인자로 넘기면 offset, limit, sort를 적용한 후 JPQLQuery를 반환받는다.     


### 장점
- getQuerydsl().applyPagination() 스프링 데이터가 제공하는 페이징을 querydsl로 편리하게 변환 가능하지만 Sort는 오류가 발생한다. [파라미터 sort]
- from으로 시작가능 queryfactory는 select()로 시작
- entityManager 제공

### 한계
- 3.x 버전을 대상으로만들고
- 4.0에 나온 JPAQueryFactory로 시작불가
- queryfactory 제공 x
- querydsl sorting을 받아서 직접 처리해야한다.




##### 출처: https://www.inflearn.com/course/lecture?courseSlug=querydsl-%EC%8B%A4%EC%A0%84&unitId=30157&tab=curriculum
