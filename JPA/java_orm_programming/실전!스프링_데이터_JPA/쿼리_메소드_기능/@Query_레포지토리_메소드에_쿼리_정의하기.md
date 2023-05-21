# @Query, 리포지토리 메소드에 쿼리 정의하기

repository method 위에 @Query를 사용하여 JPQL을 작성할 수 있다.    

메서드명으로 쿼리를 만들면 조건이 추가될 때마다 길어지지만 @Query를 사용하여 메소드명은 간략하게하며 JPQL을 이용하여 쿼리를 짤 수 있다.


```java

package study.datajpa.repository;


import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import study.datajpa.entity.Member;

import java.util.List;

public interface MemberRepository extends JpaRepository<Member, Long> {

    List<Member> findByUsernameAndAgeGreaterThan(String username, int age);
    Member findBy();

    //@Query(name = "Member.findByUsername")
    List<Member> findByUsername(@Param("username") String username);

//이름이 없는 namedQuery
    @Query("select m from Member m where m.username= :username and m.age = :age")
    List<Member> findUser(@Param("username") String username, @Param("age") int age);
}

```
@query를 사용하여 작성하면 어플리케이션 시점에 오류를 발견할 수 있다.      
메서드 이름으로 쿼리를 생성하는것은 간단할 때, 복잡해질경우 @Query로 작성한다. [ 정적쿼리일 때 ]

##### 출처: https://www.inflearn.com/course/lecture?courseSlug=%EC%8A%A4%ED%94%84%EB%A7%81-%EB%8D%B0%EC%9D%B4%ED%84%B0-JPA-%EC%8B%A4%EC%A0%84&unitId=28012&tab=curriculum
