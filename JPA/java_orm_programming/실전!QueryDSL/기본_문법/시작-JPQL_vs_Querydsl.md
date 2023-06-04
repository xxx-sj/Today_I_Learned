# JPQL VS Querydsl

이 전 JPQL 을사용하여 query를 만들 때는 아래와 같았다.
```java
package study.querydsl;

import com.querydsl.core.QueryFactory;
import com.querydsl.jpa.impl.JPAQueryFactory;
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.transaction.annotation.Transactional;
import study.querydsl.entity.Member;
import study.querydsl.entity.QMember;
import study.querydsl.entity.Team;

import javax.persistence.EntityManager;
import java.util.List;

@SpringBootTest
@Transactional
public class QuerydslBasicTest {

    @Autowired
    EntityManager em;

    JPAQueryFactory queryFactory;

    @BeforeEach
    public void before() {
        queryFactory = new JPAQueryFactory(em);
        Team teamA = new Team("teamA");
        Team teamB = new Team("teamB");
        em.persist(teamA);
        em.persist(teamB);

        Member member1 = new Member("member1", 10, teamA);
        Member member2 = new Member("member2", 20, teamA);

        Member member3 = new Member("member3", 30, teamB);
        Member member4 = new Member("member4", 40, teamB);
        em.persist(member1);
        em.persist(member2);
        em.persist(member3);
        em.persist(member4);
    }


    @Test
    public void startJQPL() {
        //member1을 찾아라.
        Member findMember = em.createQuery(
                "select m from Member m where m.username = :username", Member.class)
                .setParameter("username", "member1")
                .getSingleResult();

        Assertions.assertThat(findMember.getUsername()).isEqualTo("member1");
    }
}
```
아래는 querydsl을 사용하여 query를 짠 것이다.
```java
@Test
public void startQuerydsl() {
    QMember m = new QMember("m");

    Member findMember = queryFactory
            .select(m)
            .from(m)
            .where(m.username.eq("member1"))
            .fetchOne();

    Assertions.assertThat(findMember.getUsername()).isEqualTo("member1");
}
```

querydsl의 장점으로는 JQPL은 런타임 시점에 개발자가 잘못 작성한 sql에 의한 exception이 발생하지만 querydsl은 컴파일시점에 오류를 잡을 수 있다.
또한 paramterbinding을 자동으로 해준다.       

사용법으로는 EntityManager를 JpaQueryFactory의 인자로 넘기고, 사용하고자 하는 Qfile을 가지고 쿼리를 작성한다. 

##### 출처: https://www.inflearn.com/course/lecture?courseSlug=querydsl-%EC%8B%A4%EC%A0%84&unitId=30122&tab=curriculum&category=questionDetail
