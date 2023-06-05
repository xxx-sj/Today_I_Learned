# case문

가급적이면 db에서 해결하지 않고, db에서는 raw데이터를 필터링, 그룹핑, 필요한 계산 | 최소한의 필터링,그룹핑을 통해 데이터를 줄이는 것을 권장

```java
@Test
public void basicCase() {
    List<String> result = queryFactory
            .select(member.age
                    .when(10).then("열살")
                    .when(20).then("스무 살")
                    .otherwise("기타"))
            .from(member)
            .fetch();

    for (String s : result) {
        System.out.println("s = " + s);
    }
}

@Test
public void complexCase() {
    List<String> reuslt = queryFactory
            .select(new CaseBuilder()
                    .when(member.age.between(0, 20)).then("0~20살")
                    .when(member.age.between(21, 30)).then("21~30살")
                    .otherwise("기타")
            )
            .from(member)
            .fetch();

    for (String s : reuslt) {
        System.out.println("s = " + s);

    }
}
```

##### 출처: https://www.inflearn.com/course/lecture?courseSlug=querydsl-%EC%8B%A4%EC%A0%84&unitId=30133&category=questionDetail&tab=curriculum
