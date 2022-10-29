# proxy_pattern [프폭시 패턴]

```
특정 객체로의 접근을 제어하는 대리인(특정 객체를 대변하는 객체)을 제공한다.
```

```
지금까지 본 예제에서는 클라이언트인 모니터링 객체와 원격 객체가 직접 데이터를 주고받을 수 없으므로 프록시에서 접근을    
제어해줘야 했다. 따라서 어떤 면에서 보면 원격 프록시가 접근을 제어해서 네트워크 관련 사항을 챙겨 줬다고 할 수 있다.
```

- 원격 프록시를 써서 원격 객체로의 접근을 제어할 수 있다.
- 가상 프록시(virtual proxy)를 써서 생성하기 힘든 자원으로의 접근을 제어할 수 있다.
- 보호 프록시(protection proxy)를 써서 접근 권한이 필요한 자원으로의 접근을 제어할 수 있다.

```java
public interface ProxySubjectInterface {
    void request();
}


public class RealSubject implements ProxySubjectInterface {

    @Override
    public void request() {}
}


public class ProxySubject implements ProxySubjectInterface{

    private Subject realSubject;

    @Override
    public void request() {}
}
```

```
proxy와 RealSubject 모두 subject 인터페이스를 구현한다. 이러면 어떤 클라이언트에서든 프록시를 주제와 똑같은 식으로 다룰 수 있다.    
Realsubject는 진짜 작업을 대부분 처리하는 객체이다. proxy는 이 객체[realSubject]로의 접근을 제어한다. [원격 프록시]
proxy에서는 진짜 작업을 처리하는 객체의 레퍼런스가 들어있다. 진짜 객체가 필요하면 그 레퍼런스를 사용해서 요청을 전달한다.    
proxy에서는 realsubject의 인스턴스를 생성하거나, 그 객체의 생성 과정에 관여하는 경우가 많다.   
```

```
두 객체 모두[realSubject, proxy] subject를 구현하기에 RealSubject가 들어갈 자리에 proxy로 대체할 수 있다
진짜 작업은 RealSubject가 처리한다. proxy는 이 객체의 대변인 역할을 하면서 이 객체로의 접근을 제어한다.
클라이언트는 항상 proxy로 realSubject와 데이터를 주고 받는다.
- 같은 subject를 구현하기에 RealSubject 자리에 proxy가 들어 갈 수 있다
- proxy는 RealSubject로의 접근을 제어하는 역할도 맡는다.    
원격 시스템에서 돌아가거나 객체를 생성하는 데 비용이 많이 드는 등 어떤 방식으로든지 RealSubject로의 접근이 통제 되어야 한다면     
접근을 제어하는 객체가 필요할 수 있다.
```

### 원격(remote) 프록시 와 가상(virtual) 프록시 비교하기


##### 원격 프록시
```
원격 프록시는 다른 JVM에 들어있는 객체의 대리인에 해당하는 로컬 객체이다.
[원격 프록시 객체는 클라이언트에 속하여 로컬 객체라 부르는 듯 하다.]    
프록시의 메소드를 호출하면 그 호출이 네트워크로 전달되어 결국 원격 객체의 메소드가 호출된다.    
그리고 그 결과는 다시 프록시를 거쳐서 클라이언트에게 전달된다.
```


##### 가상 프록시
```
가상 프록시는 생성하는 데 많은 비용이 드는 객체를 대신한다. 진짜 객체가 필요한 상황이 오기 전까지    
객체의 생성을 미루는 기능을 제공한다. 객체 생성 전이나 객체 생성 도중에 객체를 대신하기도 한다.    
객체 생성이 끝나면 그냥 RealSubject에 직접 요청을 전달한다.

생성하는데 비용이 많이드는 객체가 존재할 때 정말로 해당 객체가 필요할 때 까지 미루며, 프록시가 해당 요청을 대신 처리한다.
필요할 때가 되면 프록시는 해당 객체를 생성하고 요청을 실제 객체로 넘긴다.
```






##### 출처: 헤드퍼스트 디자인패턴: 개정판
