# proxy_pattern [프록시 패턴]

```
모든 뽑기 기계의 재고와 현 상태를 알려주는 기능 추가
```

```java
class GumballMachine {

    //해당 gumballMachine의 위치
    String location;

    public GumballMachine(String location, int count) {
        this.location = location;
    }
    
    public String getLocation() {
        return location;
    }
}

//현재 상태를 가져와서 출력해주는 class
class GumballMonitor {
    GumballMachine machine;

    public GumballMonitor(GumballMachine machine) {
        this.machine = machine;
    }

    public void report() {
        System.out.println("위치 -> ");
        System.out.println("재고 -> ");
        System.out.println("현재 상태 -> ");
    }
}
```


```
1. 클라이언트 객체에서 클라이언트 보조 객체의 method()를 호출한다.    
2. 클라이언트 보조 객체는 메소드 호출 정보(인자, 메소드 이름 등)를 잘 포장해서 네트워크로 서비스 보조     
   객체에게 전달한다.
3. 서비스 보조 객체는 클라이언트 보조 객체로부터 받은 정보를 해석해서 어떤 객체의 어떤 메소드를 호출할지    
   알아낸 다음 진짜 서비스 객체의 메소드를 호출한다.
4. 서비스 객체의 메소드 실행이 끝나면 서비스 보조 객체에 어떠한 결과값이 리턴된다.
5. 서비스 보조 객체는 호출결과로 리턴된 정보를 포장해서 네트워크로 클라이언트 보조 객체에 전달한다.
6. 클라이언트 보조 객체는 리턴된 정보를 해석해서 클라이언트 객체에게 리턴한다.
   클라이언트 객체는 메소드 호출이 어디로 전달되었는지, 어디에서 리턴되었는지 알 수 없다.
```

### RMI [Remote Method Invocation]
- RMI에서 클라이언트 보조 객체는 스텁(stub) [proxy]
- 서비스 보조 객체는 스켈레톤(skeleton)이라 부른다. [proxy]

### 원격 서비스 만들기
1. 원격 인터페이스 만들기
```
원격 인터페이스는 클라이언트가 원격으로 호출할 메소드를 정의한다.    
클라이언트에서 이 인터페이스를 서비스의 클래스 형식으로 사용한다.      
스텁과 실제 서비스에 이 인터페이스를 구현해야 한다. 
```
2. 서비스 구현 클래스 만들기

```
실제 작업을 처리하는 클래스이다. 원격 메소드를 실제로 구현한 코드가 들어있는 부분
나중에 클라이언트에서 이 객체에 있는 메소드를 호출한다. [GumballMachine 클래스]
```
3. RMI 레지스트리(rmi registry)실행하기

```
rmi registry는 전화번호부와 비슷하다. 클라이언트는 이 레지스트리로부터 
프록시(스텁, 클라이언트 보조 객체)를 받아 간다.
```
4. 원격 서비스 실행하기

```
서비스 객체를 실행해야한다. 서비스를 구현한 클래스에서 서비스의 인스턴스를 만들고
그 인스턴스를 RMI 레지스트리에 등록한다. 이러면 그 서비스를 클라이언트에서 사용할 수 있다.
```


#### 1.원격 인터페이스 만들기
- java.rmi.Remote 확장 interface 만들기
```java
//remote는 marker(표식용) 인터페이스인데 메소드가 없다.
//이 인터페이스에서 원격 호출을 지원한다는 사실을 알려준다.
public interface MyRemote extends Remote {
    
}
```
- 모든 메소드는 RemoteException을 던지도록 선언하기.
```
클라이언트는 원격 인터페이스를 구현한 스텁의 메소드를 호출한다.    
각종 입출력 작업을 처리할 때 네트워크가 좋지않으면 문제가 생길 수 있다.
```

```java
package main.designPattern.proxyPattern;

import java.rmi.Remote;
import java.rmi.RemoteException;

//remote는 marker(표식용) 인터페이스인데 메소드가 없다.
//이 인터페이스에서 원격 호출을 지원한다는 사실을 알려준다.
public interface MyRemote extends Remote {

    //모든 원격 메소드 호출은 위험이 따르는 것으로 간주해야한다.
    //클라이언트에서 예외상황에 대한 대비하게 만든다.
    public String sayHello() throws RemoteException;
}

```
- 원격 메소드의 인자와 return 값은 반드시 원시 형식 또는 serializable 형식으로 선언한다.
```
모든 데이터도 네트워크로 전달되어야 하며, 직렬화로 포장된다. 직접 만든형식을 전달한다면 
serializable를 구현해야한다.
```


### 2.서비스 구현 클래스 만들기

- 서비스 클래스에 원격 인터페이스를 구현한다. (클라이언트가 인터페이스의 메소드를 호출할 것이다.)

```java
package main.designPattern.proxyPattern;

import java.rmi.RemoteException;
import java.rmi.server.RMIClientSocketFactory;
import java.rmi.server.RMIServerSocketFactory;
import java.rmi.server.UnicastRemoteObject;

public class MyRemoteImpl extends UnicastRemoteObject implements MyRemote {

    protected MyRemoteImpl() throws RemoteException {
    }

    protected MyRemoteImpl(int port) throws RemoteException {
        super(port);
    }

    protected MyRemoteImpl(int port, RMIClientSocketFactory csf, RMIServerSocketFactory ssf) throws RemoteException {
        super(port, csf, ssf);
    }

    @Override
    public String sayHello() throws RemoteException {
        return "Server say, hey";
    }
}
```

- UnicastRemoteObject를 확장한다.
```
원격 서비스 객체 역할을 하려면 객체에 '원격 객체' 기능을 추가해야한다.
UnicastRemoteObject를 확장해서, 슈퍼클래스에서 제공하는 기능으로 처리한다.
```

- RemoteException을 선언하는 생성자를 구현한다.
```
UnicastRemoteObject 에서 생성자가 RemoteException을 던진다. 이를 해결하기 위해 UnicastRemoteObject를    
선언하는 생성자를 만들어야한다. 어떤 클래스가 생성될 때 그 슈퍼클래스의 생성장도 반드시 호출되므로 슈퍼클래스     
생성자가 어떤 예외를 던진다면 서브클래스의 생성자도 그 예외를 선언해야한다.
```

- 서비스를 RMI 레지스트리에 등록한다.
```
원격 서비스를 원격 클라이언트에서 쓸 수 있게 만들어야한다. 인스턴스를 만든 다음 RMI 레지스트리에 등록하면 된다.   
(이 클래스가 실행될 때 RMI 레지스트리가 돌아가고 있어야한다.)    
서비스를 구현한 객체를 등록하면 RMI 시스템은 레지스트리에 스텁만 등록한다. 클라이언트는 스텁만 필요하니까.
서비스를 등록할 때는 [ java.rmi.Naming ] 클래스에 있는 rebind() 메소드를 사용한다.
```


```java
package main.designPattern.proxyPattern;

import java.rmi.Naming;
import java.rmi.RemoteException;
import java.rmi.server.RMIClientSocketFactory;
import java.rmi.server.RMIServerSocketFactory;
import java.rmi.server.UnicastRemoteObject;

public class MyRemoteImpl extends UnicastRemoteObject implements MyRemote {

    protected MyRemoteImpl() throws RemoteException {
    }

    protected MyRemoteImpl(int port) throws RemoteException {
        super(port);
    }

    protected MyRemoteImpl(int port, RMIClientSocketFactory csf, RMIServerSocketFactory ssf) throws RemoteException {
        super(port, csf, ssf);
    }

    @Override
    public String sayHello() throws RemoteException {
        return "Server say, hey";
    }

    public static void main(String[] args) {
        try {
            MyRemote service = new MyRemoteImpl();
            //서비스를 등록할 때는 이름을 지정해 줘야한다. 클라이어트는 그 이름으로 레지스트리를 검색한다.
            //rebind() 메소드로 서비스 객체를 결합하면 RMI는 서비스에 해당하는 스텁을 레지스트리에 추가한다.
            Naming.rebind("Remote Hello", service);

        } catch (Exception ex) {
            //do something ...
        }
    }
}
```




### 3.rmiregistry 싱행하기
- 터미널을 새로 띄워 rmiregistry를 실행한다.

### 4. 원격 서비스 실행하기
```
다른 터미널을 열고 서비스를 실행한다. 이 작업은 원격 서비스를 구현한 클래스의 main() 메소드로 실행할 수 있지만,     
별도의 클래스로부터 실행할 수도 있다. 이 예제에서는 서비스를 구현한 클래스의 main 메소드에서 바로 객체 인스턴스를 만들고    
RMI 레지스트리에 등록하였다.
```
##### 출처: 헤드퍼스트 디자인패턴: 개정판
