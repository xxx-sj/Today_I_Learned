# named 쿼리

- entity에 쿼리를 미리 짜놓는 것
```java

@Entity
@NamedQuery(
  name = "Member.findByUsername", 
  query = "select m from Member m where m.username = :username"
)
public class Member {}

List<Member> resultList = 
  em.createNamedQuery("Member.findByUsername", Member.class)
  .setParameter("username", "회원1")
  .getResultList();
```
- 정적 쿼리
- 어노테이션, XML에 정의
- 어플리케이션 로딩 시점에 초기화 후 재사용
- 어플리케이션 로딩 시점에 쿼리를 검증


##### 출처: https://www.inflearn.com/course/lecture?courseSlug=ORM-JPA-Basic&unitId=21731&tab=curriculum
