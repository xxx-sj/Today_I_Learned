# SQL Function 호출하기

- SQL function은 JPA와 같이 Dialect에 등록된 내용만 호출할 수 있다.   
- H2Dialect에 들어가보면 registerFunction에 등록된 메서드를 확인할 수 있다
- 사용법은 Experssion.stringTemplate("function('[function name]', {0}, {1}, ...)", arg1, arg2...)
function 이후에는 function에 들어가는 인자를 순서대로 넣게된다.


```java
@Test
public void sqlFunction() {
    List<String> result = queryFactory
            .select(
                    Expressions.stringTemplate(
                            "function('replace', {0}, {1}, {2})",
                            member.username, "member", "M")

            )
            .from(member)
            .fetch();

    for (String s : result) {
        System.out.println("s = " + s);
    }
}

@Test
public void sqlFunction2() {
    List<String> result = queryFactory
            .select(member.username)
            .from(member)
            .where(member.username.eq(Expressions.stringTemplate("function('lower', {0})", member.username)))
            .fetch();

    for (String s : result) {
        System.out.println("s = " + s);
    }
}
```
##### 출처: https://www.inflearn.com/course/lecture?courseSlug=querydsl-%EC%8B%A4%EC%A0%84&unitId=30142&category=questionDetail&tab=curriculum
