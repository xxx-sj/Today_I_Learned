# fecth join 기본


- JPQL 에서 성능 최적화를 위해 제공하는 기능
- 연관된 엔티티나 컬렉션을 SQL 한번에 함꼐 조회
- join fetch 명령어 사용
- fetch join ::= [LEFT [OUTER] | INNER ] JOIN FETCH 조인 경로

예시로 멤버를 조회하고 멤버에 속한 team을 조회할 때, 모든 멤버가 다른 팀 소속일 경우 **N + 1** 쿼리가 나가게 된다.
```java
select m from Member m
```

fetch join을 사용하여 해결할 수 있다.
```java
select m from Member m join fetch m.team
```
처음 member query 할 때 inner join으로 member.team_id = team_id 를 통해 모두 가져와 진다.    
따라서, team은 proxy가 아니라 실제 entity가 된다.


### fetch 조인과 DISTINCT
- jpa의 distinct는 sql의 distinct와 어플리케이션 레벨에서의 중복을 제거한다.     
- sql의 중복제거는 100% 값이 같아야만 제거되지만, 어플리케이션에서는 식별자가 중복될경우 제거해준다.
- 주로 일대다 조인에서 발생한다. team : member 조인으로 team에서 속한 member 수 만큼 로우가 조회된다.
- 반대로 다대일 같은 경우 멤버를 기준으로 조회하기 때문에 멤버만큼 혹은 그보다 적게 조회뒨다. [ member.team() == null ]

##### 출처: https://www.inflearn.com/course/lecture?courseSlug=ORM-JPA-Basic&unitId=21742&tab=curriculum
