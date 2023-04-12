# mapped superclass - 매핑 정보 상속

DB의 속성은 다르지만 반복되는 컬럼의 데이터가 나올 때 사용한다.

A table
- id
- name
- etc

B table
- id
- name
- etc


```java

@MappedSuperClass
public abstract class BaseEntity {

  private ...
  private ...
}


@Entity
class Supclass extends BaseEntity{
  ...
  ...
}

```
참고로 @Entity class 나, @MappedSuperclass로 지정한 클래스만 상속이 가능하다.


##### 출처: https://www.inflearn.com/course/lecture?courseSlug=ORM-JPA-Basic&unitId=21706&tab=curriculum&category=questionDetail
