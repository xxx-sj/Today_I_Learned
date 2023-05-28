# @EntityGraph
   
기존에 member와 team 관계에서 [ N : 1 ] member는 team을 lazy로 갖고있으며, 
member를 전체 조회 후 [findALl() = 1 ] 팀을 조회할 경우 조회된 member만큼의 team을 조회해야한다. [ N( 조회된 member 수 ) ]

JPQL을 사용할 때는 fetch조인을 통해 N + 1 문제를 해결해 왔다.
fetch 조인을 통해 member를 select해 올 때 동시에 team 또한 조회를 해왔다. 아래가 그 예시이다.   
```java
@Query("select m from Member m left join fetch m.team t")
List<Member> findMemberFetchJoin();
```

이와 동일하게 spring-data-jpa에서도 제공을 하는데, interface에 method명으로 조회를 하는 경우 [findBy ... find ..By etc..]  

```java

package study.datajpa.repository;


import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.jpa.repository.EntityGraph;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Modifying;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import study.datajpa.dto.MemberDto;
import study.datajpa.entity.Member;

import java.util.Collection;
import java.util.List;
import java.util.Optional;

public interface MemberRepository extends JpaRepository<Member, Long> {

    @Override
    @EntityGraph(attributePaths = {"team"})
    List<Member> findAll();

    @EntityGraph(attributePaths = {"team"})
    @Query("select m from Member m")
    List<Member> findMemberEntityGraph();
    
    @EntityGraph(attributePaths = {"team"})
    List<Member> findEntityGraphByUsername(@Param("username") String username);
}
```
**@EntityGraph**를 사용하여 fetch join을 할 수 있다. attributePaths에 fetch join할 entity명[property]을 작성한다 

NamedQuery처럼 **NamedEntityGraph**를 사용할 수 있다.
```java
@Entity
@Getter
@Setter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@ToString(of = {"id", "username", "age"})
@NamedQuery(
        name="Member.findByUsername",
        query ="select m from Member m where m.username = :username"
)

@NamedEntityGraph(name = "Member.all", attributeNodes = @NamedAttributeNode("team"))
public class Member {
```

### 기본적으로 JPQL을 짜서 fetch조인을하고 간단한 것들은 @ENtityGraph를 사용한다. 


##### 출처: https://www.inflearn.com/course/lecture?courseSlug=%EC%8A%A4%ED%94%84%EB%A7%81-%EB%8D%B0%EC%9D%B4%ED%84%B0-JPA-%EC%8B%A4%EC%A0%84&unitId=28019&category=questionDetail&tab=curriculum
