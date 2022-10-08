# 자동 자원반환 [try_with_resources]

##### 주로 입출력에 사용되는 클래스 중에서는 사용한 후에 꼭 닫아 줘야 하는 것들이 있다.

```java
 
try {
   //입출력처리.. inputStream
    
} catch(Exception e) {
  //do something 
} finally  {
  try {
    //자원이 닫혀있지 않다면 닫아준다.
  } catch(Exception e) {
    //do something
  }
}

```

##### try블럭과 finally 블럭에서 모두 예외가 발생하면, try블럭의 예외는 무시된다는 것


```java

//2줄이 넘어갈경우 ; 추가후 개행한다.
try(**입출력 데이터;
   입출력 데이터**) {
}catch(Exception e) {
}
```


##### try-with-resources 에서 자동으로 close()가 호출되려면 AutoCloseable 인터페이스를 구현해야 한다.

```java
package java.io;

import java.io.IOException;

public interface AutoCloseable {
    void close() throws Exception;
}
```



###### 출처[참고]: 자바의정석 3rd
