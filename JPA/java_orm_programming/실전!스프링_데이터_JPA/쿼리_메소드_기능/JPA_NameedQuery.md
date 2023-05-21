# JPA NamedQuery

### NamedQuery 사용법

1. 먼저 엔티티에 @NameQuery 어노테이션을 추가하고 그 안에 쿼리 생성한다.
```JAVA
package study.datajpa.entity;

import lombok.*;

import javax.persistence.*;

@Entity
@Getter
@Setter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@ToString(of = {"id", "username", "age"})
////////////////////////////////////
@NamedQuery(
        name="Member.findByUsername",
        query ="select m from Member m where m.username = :username"
)
////////////////////////////////
public class Member {

    @Id
    @GeneratedValue
    @Column(name = "member_id")
    private Long id;
    private String username;
    private int age;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "team_id")
    private Team team;

    public Member(String username) {
        this.username = username;
    }

    public Member(String username, int age, Team team) {
        this.username = username;
        this.age = age;
        if (team != null) {
            changeTeam(team);
        }
    }

    public Member(String username, int age) {
        this.username = username;
        this.age = age;

    }

    public void changeTeam(Team team) {
        this.team = team;
        team.getMembers().add(this);
    }
}

```

2. NameQuery를 생성한 후 repository에 method를 만들고 em.createNameQuery를 작성한다. 여기서는 
   이전에 NameQuery의 name을 사용한다.

```java
@Repository
public class MemberJpaRepository {

    @PersistenceContext
    private EntityManager em;
...

    public List<Member> findByUsername(String username) {
        return em.createNamedQuery("Member.findByUsername", Member.class)
                .setParameter("username", username)
                .getResultList();
    }
}
```

### spring-data-jpa의 NamedQuery

1. repository에 아무 method명을 작성 후 메서드 위에 @Query 어노테이션을 추가하면 name 값으로 이전의 namedQuery를 사용한다.    
```java
import org.springframework.data.repository.query.Param;

public interface MemberRepository extends JpaRepository<Member, Long> {

    @Query(name = "Member.findByUsername")
    List<Member> findByUsername(@Param("username") String username);
}
```
@Param은 NamedQuery의 파라미터가 존재할 때 사용해야 한다. import 주의!!     
@Query를 주석해도 똑같이 동작하는데, 동작의 순서는 MemberRepository의 entity <Member, Long>에서 Member.methodname [Member.findByName]으로 찾게된다.    
NamedQuery가 없으면 메서드 이름으로 쿼리를 생성한다.    


네임드쿼리는 어플리케이션 로딩시점에 파싱하기 때문에 오류를 조기에 발견할 수 있다.

**네임드쿼리는 잘 사용하지 않는다.**




##### 출처: https://www.inflearn.com/course/lecture?courseSlug=%EC%8A%A4%ED%94%84%EB%A7%81-%EB%8D%B0%EC%9D%B4%ED%84%B0-JPA-%EC%8B%A4%EC%A0%84&unitId=28010&tab=curriculum
