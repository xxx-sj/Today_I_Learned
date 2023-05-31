# Projections

- 회원의 이름만 조회하고 싶을 때 
- select절에 들어갈 필드들

### [open projection]
이 전과 동일한 spring-data-jpa를 사용하여 query하면서, return값의 제네릭으로 interface를 사용한다.     
이렇게되면 query의 select절에 interface에 정의한 get...() 식의 method를 통해 해당 property만 select하게 된다.     

```java
jpaRepository

package study.datajpa.repository;


import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.jpa.domain.Specification;
import org.springframework.data.jpa.repository.*;
import org.springframework.data.repository.query.Param;
import study.datajpa.dto.MemberDto;
import study.datajpa.entity.Member;

import javax.persistence.LockModeType;
import javax.persistence.QueryHint;
import java.util.Collection;
import java.util.List;
import java.util.Optional;

public interface MemberRepository extends JpaRepository<Member, Long>, MemberRepositoryCustom, JpaSpecificationExecutor<Member> {
  ...
    List<UsernameOnly> findProjectionsByUsername(@Param("username") String username);
}
```

```java
package study.datajpa.repository;

public interface UsernameOnly {
    String getUsername();
    Integer getAge();
}
```

```java
package study.datajpa.repository;

import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Sort;
import org.springframework.test.annotation.Rollback;
import org.springframework.transaction.annotation.Transactional;
import study.datajpa.dto.MemberDto;
import study.datajpa.entity.Member;
import study.datajpa.entity.Team;

import javax.accessibility.AccessibleStateSet;
import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;
import java.util.Arrays;
import java.util.List;
import java.util.Optional;


@SpringBootTest
@Transactional
//@Rollback(false)
class MemberRepositoryTest {

    @Autowired MemberRepository memberRepository;
    @Autowired TeamRepository teamRepository;

    @PersistenceContext
    EntityManager em;

    ...
    
    @Test
    public void projections() {
        //given
        Team teamA = new Team("teamA");
        em.persist(teamA);

        Member m1 = new Member("m1", 0, teamA);
        Member m2 = new Member("m2", 0, teamA);
        em.persist(m1);
        em.persist(m2);

        em.flush();
        em.clear();

        //when
        List<UsernameOnly> result = memberRepository.findProjectionsByUsername("m1");

        for (UsernameOnly usernameOnly : result) {
            System.out.println("usernameOnly = " + usernameOnly.getUsername());
            System.out.println("usernameOnly = " + usernameOnly.getAge());
        }
    }
}
```

```sql
select
    member0_.username as col_0_0_,
    member0_.age as col_1_0_ 
from
    member member0_ 
where
    member0_.username=?
```
이처럼 select절에 username, age 만 들어간 걸 볼 수 있다.



### open projections

위와 동일하게 interface에 method를 정의하고 spring의 @Value 어노테이션과 SPEL을 사용하여 method를 정의(?) 한다.     
```java
package study.datajpa.repository;


import org.springframework.beans.factory.annotation.Value;

public interface UsernameOnly {
//    String getUsername();
//    Integer getAge();

    @Value("#{target.username + ' ' + target.age}")
    String getUsername();
}

```

```java
package study.datajpa.repository;

import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Sort;
import org.springframework.test.annotation.Rollback;
import org.springframework.transaction.annotation.Transactional;
import study.datajpa.dto.MemberDto;
import study.datajpa.entity.Member;
import study.datajpa.entity.Team;

import javax.accessibility.AccessibleStateSet;
import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;
import java.util.Arrays;
import java.util.List;
import java.util.Optional;


@SpringBootTest
@Transactional
//@Rollback(false)
class MemberRepositoryTest {

    @Autowired MemberRepository memberRepository;
    @Autowired TeamRepository teamRepository;

    @PersistenceContext
    EntityManager em;
    
    ...

    @Test
    public void projections() {
        //given
        Team teamA = new Team("teamA");
        em.persist(teamA);

        Member m1 = new Member("m1", 0, teamA);
        Member m2 = new Member("m2", 0, teamA);
        em.persist(m1);
        em.persist(m2);

        em.flush();
        em.clear();

        //when
        List<UsernameOnly> result = memberRepository.findProjectionsByUsername("m1");

        for (UsernameOnly usernameOnly : result) {
            System.out.println("usernameOnly = " + usernameOnly.getUsername());
        }
    }
}
```
sql문을 보면 entity를 select절을 통해 이전과는 다르게 모두 가져오지만 getUsername()에서 interface에서 정의한     
username과 age를 보여주게 된다. [application]    

```java
select
    member0_.member_id as member_i1_1_,
    member0_.create_date as create_d2_1_,
    member0_.last_modified_date as last_mod3_1_,
    member0_.created_by as created_4_1_,
    member0_.last_modified_by as last_mod5_1_,
    member0_.age as age6_1_,
    member0_.team_id as team_id8_1_,
    member0_.username as username7_1_ 
from
    member member0_ 
where
    member0_.username=?
```

```java
usernameOnly = m1 0
```


### class 기반의 projection



##### 출처: https://www.inflearn.com/course/lecture?courseSlug=%EC%8A%A4%ED%94%84%EB%A7%81-%EB%8D%B0%EC%9D%B4%ED%84%B0-JPA-%EC%8B%A4%EC%A0%84&unitId=28032&category=questionDetail&tab=curriculum
