# JPQL 기본 함수

- CONCAT 합치기
- SUBSTRING 문자열 자르기
- TRIM 공백제거
- LOWER, UPPER
- LENGTH 문자의 길이
- LOCATE 위치 찾기
- ABS,SQRT, MOD
- SIZE, INDEX(JPA용도) //size collection의 크기, index [@Ordercolumn : 컬렉션의 위치 값 찾을 때 사용하지 않는것을 권장 ]

### 사용자 정의 함수 호출
- 하이버네이트는 사용전 방언에 추가해야 한다
  - 사용하는 DB방언을 상속받고, 사용자 정의 함수를 등록한다
```java
select function('group_concat', i.nmae) from Item i;
```

### 사용자 함수

//Dialect class 들어가서 확인해보기
```java

public class HyH2Dialect ectends H2Dialect {

  public MyH2Dialect() {
    registerFunction("function_name", new StandardSQLFunction("function anme", StandrardBasicTypes.STRING))
  }
}
```

##### 출처: https://www.inflearn.com/course/lecture?courseSlug=ORM-JPA-Basic&unitId=21726&tab=curriculum
