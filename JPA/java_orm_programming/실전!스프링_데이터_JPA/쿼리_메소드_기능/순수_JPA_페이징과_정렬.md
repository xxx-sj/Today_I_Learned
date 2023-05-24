# 순수 JPA 페이징과 정렬


일반 JPA에서 JQPL을 사용하여 페이징과 정렬을 할 수 있다.

```java

package study.datajpa.repository;

import org.springframework.stereotype.Repository;
import org.springframework.transaction.annotation.Transactional;
import study.datajpa.entity.Member;

import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;
import java.util.List;
import java.util.Optional;

@Repository
public class MemberJpaRepository {

    @PersistenceContext
    private EntityManager em;

    /**
     * 나이가 10살 : 검색조건
     * 이름으로 내림차순: 정렬조건
     * 첫 번째 페이지, 페이지당 보여줄 데이터는 3건: 페이징 조건
     */
    public List<Member> findByPage(int age, int offset, int limit) {
    // parameter age에 해당하는 member, 그리고 username 기준 desc
        return em.createQuery("select m from Member m where m.age = :age order by m.username desc")
                .setParameter("age", age)
                .setFirstResult(offset) // 어디서 부터 가져올 것인가 offset
                .setMaxResults(limit) // 얼만큼 가져올 것인가, limit
                .getResultList();
    }

    //totalcount 몇 번째 페이지인지에 대한 정보
    public long totalCount(int age) {
        return em.createQuery("select count(m) from Member m where m.age = :age", Long.class)
                .setParameter("age", age)
                .getSingleResult();
    }
}
```
totalCount는 현재 페이지가 몇 번째 페이지인지에 대한 정보를 위한 조회이다[ page / count ]

그에 대한 test code

```java

@Test
public void paging() {
    //given
    memberJpaRepository.save(new Member("member1", 10));
    memberJpaRepository.save(new Member("member2", 10));
    memberJpaRepository.save(new Member("member3", 10));
    memberJpaRepository.save(new Member("member4", 10));
    memberJpaRepository.save(new Member("member5", 10));

    int age = 10;
    int offset = 0;
    int limit = 3;

    //when
    //페이징된 컨텐츠
    List<Member> members = memberJpaRepository.findByPage(age, offset, limit);
    long totalCount = memberJpaRepository.totalCount(age);

    //페이지 계산 공식 적용...
    //totalPager = totalCount / size...
    //마지막 페이지
    // 최초 페이지

    //then
    Assertions.assertThat(members.size()).isEqualTo(3);
    Assertions.assertThat(totalCount).isEqualTo(5);
}
```

##### 출처: https://www.inflearn.com/course/lecture?courseSlug=%EC%8A%A4%ED%94%84%EB%A7%81-%EB%8D%B0%EC%9D%B4%ED%84%B0-JPA-%EC%8B%A4%EC%A0%84&unitId=28016&tab=curriculum
