# 프로젝션과 결과 반환 - DTO 조회

### 순수 JPA 에서 DTO 조회 [JPQL]
```java
package study.querydsl.dto;

import lombok.Data;

@Data
public class MemberDto {

    private String username;
    private int age;

    public MemberDto(String username, int age) {
        this.username = username;
        this.age = age;
    }
}

```

```java
@Test
public void findDtoByJPQL() {
    List<MemberDto> resultList = em.createQuery("select new study.querydsl.dto.MemberDto(m.username, m.age) " +
            "from Member m", MemberDto.class)
            .getResultList();

    for (MemberDto memberDto : resultList) {
        System.out.println("memberDto = " + memberDto);
    }
}
```
- new 명령어를 사용해야하며,
- DTO package 명을 다 적어줘야 한다.
- 생성자 방식만을 지원한다.


### Querydsl빈 생성
- 프로퍼티 접근
- 필드 직접 접근
- 생성자 사용

```java
//getter setter
@Test
public void findDtoBySetter() {
    List<MemberDto> result = queryFactory
            .select(Projections.bean(MemberDto.class,
                    member.username,
                    member.age))
            .from(member)
            .fetch();

    for (MemberDto memberDto : result) {
        System.out.println("memberDto = " + memberDto);
    }
}

//field
@Test
public void findDtoByField() {
    List<MemberDto> result = queryFactory
            .select(Projections.fields(MemberDto.class,
                    member.username,
                    member.age))
            .from(member)
            .fetch();

    for (MemberDto memberDto : result) {
        System.out.println("memberDto = " + memberDto);
    }
}

//constructor
@Test
public void findDtoByConstructor() {
    List<MemberDto> result = queryFactory
            .select(Projections.constructor(MemberDto.class,
                    member.username,
                    member.age))
            .from(member)
            .fetch();

    for (MemberDto memberDto : result) {
        System.out.println("memberDto = " + memberDto);
    }
}
```
생성자를 통한 select는 **contructor의 인자 순서에 맞게** 설정해 주어야 한다.
생성자를 통한 select에서는 인자의 이름이 아닌, type으로 들어가게된다.    
ex) username, age // 가 아닌 -> string, age 로 들어가게 된다.


### DTO와 column명이 다를 때 
DTO와 column명이 다를 때 매칭해 주기 위해 as를 통해 별칭을 주어 맞춰줘야 한다. 
```java
package study.querydsl.dto;

import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
public class UserDto {

    private String name;
    private int age;
}
```

```java
@Test
public void findUserDto() {
    List<UserDto> result = queryFactory
            .select(Projections.fields(UserDto.class,
                    member.username.as("name"),
                    member.age))
            .from(member)
            .fetch();

}
```

필드나, 서브쿼리에 별칭을 적용할 때 ExpressionUtils.as()를 사용한다.
```java
@Test
public void findUserDto() {
    QMember memberSub = new QMember("memberSub");

    List<UserDto> result = queryFactory
            .select(Projections.fields(UserDto.class,
//                  ExpressionUtils.as(member.username, "name")
                    member.username.as("name"),

                    ExpressionUtils.as(JPAExpressions
                    .select(memberSub.age.max())
                    .from(memberSub), "age")
            ))
            .from(member)
            .fetch();

}
```




##### 출처: https://www.inflearn.com/course/lecture?courseSlug=querydsl-%EC%8B%A4%EC%A0%84&unitId=30137&category=questionDetail&tab=curriculum
