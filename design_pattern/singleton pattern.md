Singleton pattern [싱글턴 패턴]

### 클래스 인스턴스를 하나만 만들고, 그 인스턴스로의 전역 접근을 제공한다.

- 스레드 풀
- 캐시
- 대화상자
- 사용자 설정
- 레지스트리 설정
- 로그 기록용 객체
- 디바이스 드라이버


```java
// 단순 싱글턴 사용 x
class SimpleSingleton {
    private static SimpleSingleton singleton;

    //...

    private SimpleSingleton() {}

    public static SimpleSingleton getInstance() {
        if (singleton == null) {
            singleton = new SimpleSingleton();
        }

        return singleton;
    }

    //...
}
```

##### 인스턴스를 리턴하는 메소드에서 동기화하지 않으면 멀티스레드 환경에서
##### 동시에 메소드에 접근해 인스턴스가 여러개 생길 수 있다.


```java
...
public static synchronized SimpleSingleton getInstance() {
        if (singleton == null) {
            singleton = new SimpleSingleton();
        }

        return singleton;
    }
  ...

```

```
동기화 키워드 작성 시 속도가 중요하지 않다면 두어도 되겠지만,   
성능이 약 100배정도 저하될 수 있다. 또한 해당 메소드가 어플리케이션에서    
병목으로 작용한다면 다른방법을 생각해야 한다.
```

##### 동기화 할 때의 속도 이슈
##### 동기화는 처음을 제외하면 불필요한 오버헤드만 증가시킬 수 있다.



##### 처읍투너 인스턴스를 생성하는 것

```java

class SimpleSingleton {
    private static SimpleSingleton singleton = new SimpleSingleton();

    //...

    private SimpleSingleton() {}

    public static SimpleSingleton getInstance() {
        return singleton;
    }

    //...
}
```


##### DCL(dobule-checked locking)을 사용하여 동기화되는 부분을 줄이기
##### 인스턴스가 생성되어 있는지 확인한 다음 생성되어 있지 않았을 때만 동기화할 수 있습니다
##### 이러면 처음에만 동기화 하고 이 후 부터는 동기화 하지 않는다.


```java
class SimpleSingleton {
    private volatile static SimpleSingleton singleton;

    //...

    private SimpleSingleton() {}

    public static SimpleSingleton getInstance() {
        if (singleton == null) {
        //동기화 블록
            synchronized (SimpleSingleton.class) {
              if (singleton == null) {
                singleton = new SimpleSingleton();
              }
            }
        }

        return singleton;
    }

    //...
}
```

###### 동기화문제, 클래스 로딩 문제, 리플렉션, 역직렬화와 직렬화 문제 등은 enum으로 싱글턴을 생성해서
###### 해결할 수 있다


```java

public enum Singleton {
  UNIQUE_INSTANCE;
  //etc field
}

public class SingleTonClass {
  public static void main(String[] args) {
    Singleton singleton = Singleton.UNIQUE_INSTANCE;
    //싱글턴 사용
  }
}



```


###### 출처: 헤드퍼스트 디자인패턴:개정판
