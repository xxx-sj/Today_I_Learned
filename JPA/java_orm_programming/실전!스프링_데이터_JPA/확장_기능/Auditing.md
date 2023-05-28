# Auditing

- 엔티티 생성, 변경할 때 변경한 사람과 시간을 추적하고 싶을 때
- 등록일
- 수정일
- 등록자
- 수정자


### 순수 JPA 사용
```java

package study.datajpa.entity;

import lombok.Getter;

import javax.persistence.Column;
import javax.persistence.MappedSuperclass;
import javax.persistence.PrePersist;
import javax.persistence.PreUpdate;
import java.time.LocalDateTime;

@MappedSuperclass
@Getter
public class JpaBaseEntity {

    @Column(updatable = false)
    private LocalDateTime createDate;

    private LocalDateTime updateDate;

    //persist 전 event
    @PrePersist
    public void prePersist() {
        LocalDateTime now = LocalDateTime.now();
        this.createDate = now;
        this.updateDate = now;
    }

//update 전
    @PreUpdate
    public void preUpdate() {
        this.updateDate = LocalDateTime.now();
    }

}


///////////////////////////////////////////
MemberEntity.java

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
public class Member extends JpaBaseEntity {...}
```

```java
@Test
public void JpaEventBaseEntity() throws Exception {
    //given
    Member member = new Member("member1");
    memberRepository.save(member); //@PrePersist

    Thread.sleep(100); //create, update 시간 차이를 주기위해
    member.setUsername("member2");

    em.flush(); //@preUpdate
    em.clear();

    //when
    Member findMember = memberRepository.findById(member.getId()).get();

    //then
    System.out.println("findMember.getCreateDate() = " + findMember.getCreateDate());
    System.out.println("findMember.getUpdateDate() = " + findMember.getUpdateDate());
}
```

- JPA주요 이벤트 어노테이션
- @PrePersist, @PostPersist
- @PreUpdate, @PostUpdate


### Spring-data-jpa 사용 
1. @EnableJpaAudting 추가하기 -> 스프링 부트 설정 클래스에 적용해야한다.
```java
package study.datajpa;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.data.jpa.repository.config.EnableJpaAuditing;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;

@SpringBootApplication
@EnableJpaAuditing //
//@EnableJpaRepositories(basePackages = "study.datajpa.repository")
public class DataJpaApplication {

	public static void main(String[] args) {
		SpringApplication.run(DataJpaApplication.class, args);
	}

}
```

적용한 BaseEntity를 아래와 같이 작성한다.
```
package study.datajpa.entity;

import lombok.Getter;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.Column;
import javax.persistence.EntityListeners;
import javax.persistence.MappedSuperclass;
import java.time.LocalDateTime;

//이벤트 기반으로 동작하는 것을 알려주기 위해
@EntityListeners(AuditingEntityListener.class)
@MappedSuperclass
@Getter
public class BaseEntity {

    @CreatedDate
    @Column(updatable = false)
    private LocalDateTime createDate;

    @LastModifiedDate
    private LocalDateTime lastModifiedDate;
}

```

### 등록자, 수정자
이 전 코드는 등록일과 수정일만을 작성하였는데, 등록자와 수정자를 넣을 수 있다.
```java
package study.datajpa.entity;

import lombok.Getter;
import org.springframework.data.annotation.CreatedBy;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedBy;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.Column;
import javax.persistence.EntityListeners;
import javax.persistence.MappedSuperclass;
import java.time.LocalDateTime;

//이벤트 기반으로 동작하는 것을 알려주기 위해
@EntityListeners(AuditingEntityListener.class)
@MappedSuperclass
@Getter
public class BaseEntity {

    @CreatedDate
    @Column(updatable = false)
    private LocalDateTime createDate;

    @LastModifiedDate
    private LocalDateTime lastModifiedDate;

    @CreatedBy
    @Column(updatable = false)
    private String createdBy;

    @LastModifiedBy
    private String lastModifiedBy;
}
```
여기서 등록자, 수정자는 추가로 bean을 만들어줘야 하는데, 이 bean이 하는 역할은 등록자나 수정자를 넣을 때 해당 bean을 호출하여      
값을 전달한다.    
```java
package study.datajpa;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.data.domain.AuditorAware;
import org.springframework.data.jpa.repository.config.EnableJpaAuditing;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;

import java.util.Optional;
import java.util.UUID;

@SpringBootApplication
@EnableJpaAuditing
//@EnableJpaRepositories(basePackages = "study.datajpa.repository")
public class DataJpaApplication {

	public static void main(String[] args) {
		SpringApplication.run(DataJpaApplication.class, args);
	}

	@Bean
	public AuditorAware<String> auditorProvider() {
		//현재 session의 id를 가져와 설정한다.
		//spring-security라면 context에서 가져오기.
		return () -> Optional.of(UUID.randomUUID().toString());
    
//		return new AuditorAware<String>() {
//			@Override
//			public Optional<String> getCurrentAuditor() {
//				return Optional.of(UUID.randomUUID().toString());
//			}
//		};
	}
}
```
여기서 auditorProvider를 통해 등록자, 수정자의 값을 넣는데, session 정보에서나, spring-security를 쓴다면 context에서 유저 정보를      
가져와 값을 설정한다.     

BaseEntity와 BaseTimeEntity 를 분리하는것도 권장한다.

##### 출처: https://www.inflearn.com/course/lecture?courseSlug=%EC%8A%A4%ED%94%84%EB%A7%81-%EB%8D%B0%EC%9D%B4%ED%84%B0-JPA-%EC%8B%A4%EC%A0%84&unitId=28023&category=questionDetail&tab=curriculum
