# web 확장 - 페이징과 정렬 

단순 페이징을 Pageable을 인자로 받아 사용할 수 있다. 
기본적으로 page = 0, size = 20 이다.

```java
//controller 
@GetMapping("/members")
public Page<Member> list(Pageable pageable) {
  Page<Member> page = memberRepository.findAll(pageable);
  return page;
}
```
ex) /members?page=0&size=3&sort=id,desc&sort=username,desc

### Pageable 글로벌 기본값 변경
```java
applocation.yml
spring:
  data:
    web:
      pageable:
        default-page-size: 10
        max-page-size: 2000
```

### 개별설정
```java
controller

@GetMapping("/members")
public Page<MemberDto> list(@PageableDefault(size = 5, sort = "username") Pageable pageable) {
    Page<Member> page = memberRepository.findAll(pageable);
    Page<MemberDto> map = page.map(m -> new MemberDto(m.getId(), m.getUsername(), null));
    return map;
}
```

- 페이징 정보가 둘 이상이면 접두사로 구분
- @Qualifier에 접두사명 추가 {접두사명}_xxx
- /members?member_page=0&order_page=1

```java
public String list(
@Qualifier("member") Pageable memberPageable,
@Qualifier("order) Pageable orderPageable, ...
) {}
```

##### 출처: https://www.inflearn.com/course/lecture?courseSlug=%EC%8A%A4%ED%94%84%EB%A7%81-%EB%8D%B0%EC%9D%B4%ED%84%B0-JPA-%EC%8B%A4%EC%A0%84&unitId=28025&category=questionDetail&tab=curriculum
