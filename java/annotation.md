# annotation [애너 테이션]



```
문서와 소스코드를 하나의 파일로 관리하기 하는 것이 좋겠다고 개발자들은 생각했따. 그래서 소스코드 주석에 정보를 저장하고,   
소스코드의 주석으로부터 HTML 문서를 생성해내는 프로그램 (javadoc.exe)를 만들어서 사용했다.
미리 정의된 태그들을(@) 이용해서 주석 안에 정보를 저장하고, javadoc.ext 이라는 프로그램이 이 정보를 읽어서 문서를 작성하는데 사용한다.   
이 기능을 응용하여, 프로그램의 소스코드 안에 다른 프로그램을 위한 정보를 미리 약속된 형식으로 포함시킨 것이 바로 애너테이션이다.    
애너테이션은 주석처럼 프로그래밍 언어에 영향을 미치지 않으면서도 다른 프로그램에게 유용한 정보를 제공 할 수 있다.
```


#### 표준 애너테이션

@SuppressWarnings - 컴파일러의 특정 경고메시지가 나타나지 않게 해준다.
@SageVarargs - 지네릭스 타입의 가변인자에 사용한다.
@FuncationalInterface - 함수형 인터페이스라는 것을 알린다.
@Native - native메서등서 참조되는 상수 앞에 붙인다.
[meta]@Target - 애너테이션이 적용가능한 대상을 지정하는데 사용한다.
    - ANNOTAION_TYPE 
    - CONSTRUCTOR
    - FIELD : 기본형
    - LOCAL_VARIABLE
    - METHOD
    - PACKAGE
    - PARAMETER
    - TYPE : class enum...
    - TYPE_PARAMETER
    - TYPE_USE : 참조변수에
[meta]@Documented - 애너테이션 정보가 javadoc으로 작성된 문서에 포함되게 한다.
[meta]@Inherited - 애너테이션이 자손 클래스에 상속되도록 한다.
  - 해당 어노테이션이 붙어있으면 조상 클래스에서 해당 어노테이션을 사용하는 어노테이션을 붙였을 경우 자손에서도 사용한것으로 처리된다.
[meta]@Retention - 애너테이션이 유지되는 범위를 지정하는데 사용된다.
    - SOURCE : 소스 파일에만 존재, 클래스파일에는 존재하지 않음
    - CLASS : 클래스 파일에 존재, 실행시에 사용불가, 기본값
    - RUNTIME: 클래스 파일에 존재, 실행시에 사용가능
[meta]@Repeatable - 애너테이션을 반복해서 적용할 수 있게 한다.


##### 어노테이션 타입 정의하기
```
@ 기호를 붙이는 것을 제외하면 인터페이스를 정의하는 것과 동일하다.

@interface 어노테이션 이름 {
  타입 요소 이름();
}

@override 는 어노테이션이고 Override는 어노테이션 타입이다.
```

```
어노테이션 내에 선언된 메서드를 어노테이션의 요소 라고 한다.
```

```java
public @interface TestInfo {
    int count();
    String testedBy();
    String[] testTools();
    TestType testType(); //enum class
    DataTime testDate(); // 자신이 아닌 다른 어노테이션을 포함 할 수 있다 @DateTime
}

@interface DateTime {
    String yymmdd();
    String hhmmss();
}

```

```
어노테이션의 요소는 반환값이 있고[int count()] 매개변수는 없는 추상 메소드의 형태를 가진다.    
어노테이션을 사용 시 요소들의 값을 빠짐없이 지정해주어야 한다.
```

```java
public @interface TestInfo {
    int count();
    String testedBy();
    String[] testTools();
    TestType testType(); //enum class
    DateTime testDate(); // 자신이 아닌 다른 어노테이션을 포함 할 수 있다 @DateTime
}

@interface DateTime {
    String yymmdd();
    String hhmmss();
}


@TestInfo(
        count = 3, testedBy = "js",
        testTools = {"JUnit"},
        testType = TestType.First,
        testDate = @DateTime(yymmdd = "123124", hhmmss="231242")
)
class NewClass {}

```

- 어노테이션은 기본값을 가질 수 있다 [default]
```java

public @interface TestInfo {
    int count() default 1;
    String testedBy();
    String[] testTools();
    TestType testType(); //enum class
    DateTime testDate(); // 자신이 아닌 다른 어노테이션을 포함 할 수 있다 @DateTime
}
```

- 또한 요소가 하나뿐이고 이름이 value인 경우 생략 가능하다.

##### 출처: 자바의 정석
