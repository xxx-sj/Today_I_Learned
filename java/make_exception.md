사용자정의 예외 만들기


##### 예외를 선택적으로 처리할 수 있게 RuntimeException을 받을수도 있다.
```java
class SampleException extends Exception {
    private final int ERROR_CODE;

    SampleException(String msg, int errorCode) {
        super(msg);
        ERROR_CODE = errorCode;
    }

   public SampleException(String msg) {
       this(msg, 9920);
   }

   public int getErrorCode() {
        return ERROR_CODE;
   }
}
```


###### 출처:자바의정석
