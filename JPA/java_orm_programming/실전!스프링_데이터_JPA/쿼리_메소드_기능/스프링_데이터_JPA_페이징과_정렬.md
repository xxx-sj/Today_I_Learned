# 스프링 데이터 JPA 페이징과 정렬

### 세세 팁
더보기 버튼 같은 경우 데이터 가져오는건 11개를 가져와서, 10개만 보여주고 1개가 더 있다면 더 보기 버튼을 활성화 한다.    
계속 이런식으로 가져오다가 나눠 떨어지거나 부족해질 때 더보기 버튼이 사라진다.      

- org.springframework.data.domain.Sort: 정렬기능
- org.springframework.data.domain.Pageable: 페이징 기능 (내부에 sort)

반환타입
- org.springframework.data.domain.Page: 추가 count 쿼리 결과를 포함하는 페이징
- org.springframework.data.domain.Slice: 추가 count 쿼리 없이 다음 페이지만 확인 가능
- List : 추가 count 쿼리 없이 결과만 반환

반환타입에 따라 count를 조회할지 말지를 결정한다.

### 스프링 데이터 JPA 페이징

```java
Page<Member> findByusername(String name, Pageable pageable); 
Slice<Member> findByusername(String name, Pageable pageable); 
```

1. 먼저 repository에 method를 정의한다.
```java
package study.datajpa.repository;


import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import study.datajpa.dto.MemberDto;
import study.datajpa.entity.Member;

import java.util.Collection;
import java.util.List;
import java.util.Optional;

public interface MemberRepository extends JpaRepository<Member, Long> {

    //pageable -> 페이지 조건
    Page<Member> findByAge(int age, Pageable pageable);
}
```

사용 할 때 PageRequest를 만들어서 [ pageable의 구현체 ] 인자로 전달한다.

```java

@Test
public void paging() {
    //given
    memberRepository.save(new Member("member1", 10));
    memberRepository.save(new Member("member2", 10));
    memberRepository.save(new Member("member3", 10));
    memberRepository.save(new Member("member4", 10));
    memberRepository.save(new Member("member5", 10));

    int age = 10;
    // of(page, size, sort) // 0 page 3개 를 가져오면서 username을 기준으로 DESC 정렬
    PageRequest pageRequest = PageRequest.of(0, 3, Sort.by(Sort.Direction.DESC, "username"));

    //when
    //페이징된 컨텐츠
    Page<Member> page = memberRepository.findByAge(age, pageRequest);

    //then
    List<Member> content = page.getContent();
    long totalElements = page.getTotalElements();

    //아이템의 수
    Assertions.assertThat(content.size()).isEqualTo(3);
    // 총 갯수 count
    Assertions.assertThat(page.getTotalElements()).isEqualTo(5);
    //page의 넘버
    Assertions.assertThat(page.getNumber()).isEqualTo(0);
    // page 총 갯수
    Assertions.assertThat(page.getTotalPages()).isEqualTo(2);
    // 현 페이지가 첫 번째인가.
    Assertions.assertThat(page.isFirst()).isTrue();
    // 다음 페이지가 있는가
    Assertions.assertThat(page.hasNext()).isTrue();

}
```

페이징에서 중요한건 totalcount의 데이터가 많아질수록 느려진다.      
스프링 데이터 JPA에서도 totalcount query 분리를 제공한다.      
```java
//pageable -> 페이지 조건
@Query(value = "select m from Member m left join m.team t",
        countQuery = "select count(m.username) from Member m")
Page<Member> findByAge(int age, Pageable pageable);
```
위 예시에서 countQuery를 보면, 일반 query에서 left outer join을 하고 있기 때문에 count에서는 member만을 조회해도 된다. [ team join은 빼도 된다. ]     
join을 제거함으로써 성능을 향상 시킨다.      

Sorting 조건이 복잡해지면 JQPL을 직접 사용하는것을  권장      

단순 3건 조회 method명으로 = findTop3ByAge(age)       


### 실무에서는...
entity를 controller에서 직접 반환해서는 안된다.     
DTO로 변환해서 반환해야 한다.  entity가 변경되면 api 스펙이 변경되기 때문에     
page에는 map()가 존재하는데 stream().map()과 같이 iterate하다.    

```java
//when
//페이징된 컨텐츠
Page<Member> page = memberRepository.findByAge(age, pageRequest);

Page<MemberDto> toMap = page.map(member -> new MemberDto(member.getId(), member.getUsername(), null));
```





##### 출처: https://www.inflearn.com/course/lecture?courseSlug=%EC%8A%A4%ED%94%84%EB%A7%81-%EB%8D%B0%EC%9D%B4%ED%84%B0-JPA-%EC%8B%A4%EC%A0%84&unitId=28017&tab=curriculum
