# JPA Hint and Lock

### JPA Hint
- JPA 쿼리 힌트 (SQL 힌트가 아니라 JPA 구현체에게 제공하는 힌트)

jpa에서는 더티체킹으로 인해 영속성 컨텍스트에 속한 entity의 값을 변경 후 transaction이 종료되면 변경된 값을 update하는    
query가 나가게 된다. 여기서 만약 값만 변경하고 더티체킹을 하지 않고 싶을 때 [스냅샷을 만들고 싶지 않을 때 ] 사용할 수 있는 hint가    
readonly이다 .
```java
package study.datajpa.repository;


import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.jpa.repository.*;
import org.springframework.data.repository.query.Param;
import study.datajpa.dto.MemberDto;
import study.datajpa.entity.Member;

import javax.persistence.QueryHint;
import java.util.Collection;
import java.util.List;
import java.util.Optional;

public interface MemberRepository extends JpaRepository<Member, Long> {

    @QueryHints(value = @QueryHint(name = "org.hibernate.readOnly", value="true"))
    Member findReadOnlyByUsername(String username);
}
```

### Lock
JPA가 제공하는 Lock을 편하게 사용할 수 있게 spring-data-jpa에서 **@Lock** 이라는 어노테이션을 제공한다.
```java
@Lock(LockModeType.PESSIMISTIC_WRITE)
List<Member> findLockByUsername(String username);
```

실시간 트래픽이 많을경우 lock 쓰는것을 권장하지 않는다. 돈과 같이 중요한 로직같은 경우에 사용 

##### 출처: https://www.inflearn.com/course/lecture?courseSlug=%EC%8A%A4%ED%94%84%EB%A7%81-%EB%8D%B0%EC%9D%B4%ED%84%B0-JPA-%EC%8B%A4%EC%A0%84&unitId=28020&category=questionDetail&tab=curriculum
