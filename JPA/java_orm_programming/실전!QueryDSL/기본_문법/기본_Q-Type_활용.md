# 기본 Q-Type 활용

Q클래스 인스턴스를 사용하는 2가지 방법
```java

QMember qMember = new QMember("m"); //별칭 직접 지정
QMember qMember = QMember.member; //기본 인스턴스 사용
```

```java

    
@Test
public void startQuerydsl() {
//        QMember m = new QMember("m");
//        QMember m = QMember.member;


    Member findMember = queryFactory
            .select(member)
            .from(member)
            .where(member.username.eq("member1"))
            .fetchOne();

    Assertions.assertThat(findMember.getUsername()).isEqualTo("member1");
}
```
가장 권장되는 방법 import static 을 통해 member만을 사용하는 것.     

querydsl은 PQL 의 빌더 역할을 하는것이기 때문에 작성한 코드는 JQPL이 되는 것이다.      

QMember를 생성할 때 인자로 alias를 주게 되는데, alias는 query에서 Member에 대한 alias를 지정하게 된다.
같은 테이블을 join할 때는 alias가 겹치기 때문에 QEntity를 각각 생성하여 alias를 줘야한다.


##### 출처: https://www.inflearn.com/course/lecture?courseSlug=querydsl-%EC%8B%A4%EC%A0%84&unitId=30123&tab=curriculum&category=questionDetail
