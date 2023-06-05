# 프로젝션과 결과 반환 - @QueryProjection

@QueryProjection은 DTO의 생성자 위에 선언하여 사용한다.

```java

public class Dto() {

  private String property1;
  private int property2;
  
  @QueryProjection
  public Dto(String property1, int property2) {
    this.pro ...
    this.pro2...
  }
}

```

```java
@Test
public void findDtoByQueryProjection() {
    List<Dto> result = queryFactory
            .select(new Dto(member.property1, member.property2))
            .from(member)
            .fetch();

    for (Dto dto : result) {
        System.out.println("dto = " + dto);
    }
}
```
장점: 컴파일 시점에 오류를 발견할 수 있다. 생성자를 통해 select를 하는데 QDto를 만들면 오류를 줄일 수 있다.
단점: querydsl 을 사용하기 때문에 순수한 객체가 될 수 없다.
