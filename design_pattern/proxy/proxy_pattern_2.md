# proxy_pattern [프록시 패턴]

### 1에 이어서...

##### 작동방식
1. 클라이언트에서 RMI 레지스트리를 룩업한다.
```java
Naming.lookup("rmi://127.0.0.1/RemoteHello")
```
2. RMI 레지스트리에서 스텁 객체를 리턴한다. 스텁 객체는 lookup() 메소드의 리턴 값으로 전달되며(클라이언트에게),    
   RMI에서는 그 스텁을 자동으로 역직렬화한다. 이 때 (rmic에서 생성해 준) 스텁 클래스는 반드시 클라이언트에만 있어야 한다.
   그 클래스가 없으면 역직렬화를 할 수 없다.
   
3. 클라이언트는 스텁의 메소드를 호출한다. 스텁이 진짜 서비스 객체라고 클라이언트는 생각하게 된다.


##### 클라이언트 코드 살펴보기 

```java
public class MyRemoteClient {
    public static void main(String[] args) {
        new MyRemoteClient().go();
    }

   //서버에 요청하는 메소드
    public void go() {

        //1. lookup을 통해 stub 객체를 리턴 받고 (클라이언트는 실제 서비스라 생각하게 된다.)
        //2. 리턴받은 객체에 sayHello() 메소드 호출. (remote 에러 처리 필요)
        try {
                                                         //ip 주소 또는 호스트 이름이 필요하다.
            MyRemote service = (MyRemote) Naming.lookup("rmi://127.0.0.1/RemoteHello");
                                                                           //서비스 결합(재결합)할 때 지정했던 이름도 필요

            String s = service.sayHello(); //일반 메소드 호출방식과 같다. [remoteException 에러 처리 대비 필요]

            System.out.println(s);
        }catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
1. Naming.rebind()를 호출해서 서비스를 등록할 때 RMI 레지스트리가 돌아가고 있어야 한다. [원격 서비스를 돌리기 전에 rmiregistry 실핼 필요]
2. 실행하기 전까지는 직렬화가 되지 않았따는 사실을 알 수 없다. 컴파일러에서는 잡아 낼 수 없는 문제이기 때문이다 [인자와 리턴형식을 직렬화 필요]



### GumballMachine 클래스를 원격 서비스로 바꾸기
1. GumballMachine의 원격 인터페이스 만들기
   인터페이스는 원격 클라이언트에서 호출할 수 있는 메소드를 정의한다.
2. 인터페이스의 모든 리턴 형식을 직렬화 할 수 있는지 확인한다.
3. 구상 클래스에서 인터페이스를 구현한다.


```java
//원격 인터페이스
public interface GumballMachineRemote extends Remote {
    public int getCount() throws RemoteException;
    public String getLocation() throws RemoteException;

    /**
     * 모든 리턴 형식은 원시 형식 또는 serializable이어야 한다.
     * 지원해야 하는 메소드 모두 remoteException을 던질 수 있다.
     */
    public State getState() throws RemoteException;
}
```

```java
public interface State extends Serializable {
    public void insertQuarter();
    public void ejectQuarter();
    public void turnCrank();
    public void dispense();
}
```

##### 모든 State객체에는 뽑기 기계의 메소드를 호출하거나 상태를 변경할 때 사용하는 뽑기 기계의 레퍼런스가 들어있다.
##### 하지만 State객체가 전솔될 때 GumballMachine 클래스도 전부 직렬화해서 같이 보내는 것도 바람직하지 않다.
##### 아래와 같이 수정한다.

```java
package main.designPattern.proxyPattern;

public class NoQuarterState implements State {
    private static final long serialVersionUiD = 2L;
    /**
     * state를 구현하는 모든 클래스에서 GumballMachine 인스턴스 변수를 선언하는 부분에
     * transient 키워드를 추가한다.
     * 이러면 jvm에서는 그 필드를 직렬화 하지 않는다.
     * 하지만, 객체를 직렬화해서 전송받은 후에 이 필드를 호출하면 안 좋은 일이 생길 수 있다.
     */
    transient GumballMachine gumballMachine;
    @Override
    public void insertQuarter() {

    }

    @Override
    public void ejectQuarter() {

    }

    @Override
    public void turnCrank() {

    }

    @Override
    public void dispense() {

    }
}
```

##### GumballMachine 클래스를 네트워크로 들어온 요청을 처리하는 서비스로 수정

```java
package main.designPattern.proxyPattern;

import java.rmi.RemoteException;
import java.rmi.server.UnicastRemoteObject;

public class RemotedGumballMachine extends UnicastRemoteObject implements GumballMachineRemote {

    private int count;
    private State state;
    private String location;

    private static final long serialVersionUID = 2L;

    protected RemotedGumballMachine() throws RemoteException {

    }

    @Override
    public int getCount() throws RemoteException {
        return count;
    }

    @Override
    public String getLocation() throws RemoteException {
        return location;
    }

    @Override
    public State getState() throws RemoteException {
        return state;
    }
}

```


##### RMI 레지스트리 등록하기 [server에서 등록한다 (stub)]

```java
package main.designPattern.proxyPattern.rmi;

import main.designPattern.proxyPattern.RemotedGumballMachine;

import java.net.MalformedURLException;
import java.rmi.Naming;
import java.rmi.RemoteException;

public class GumballMachineTestDrive {

    public static void main(String[] args) {
        //service[server] 객체
        RemotedGumballMachine gumballMachine = null;
        int count;

        //기본적으로 name 과 invertory를 인자로 받는다.
        if (args.length < 2) {
            System.out.println("GumballMachine <name> <inventory>");
            //종료
            System.exit(1);
        }

        /**
         * args[0] = name [location]
         * args[1] = count [inventory]
         */
        try {
            count = Integer.parseInt(args[1]);
            //service 객체 생성
            gumballMachine = new RemotedGumballMachine(args[0], count);
            //rmi registry 등록한다 "//location/gumballmachine[registry name], gumballMachine[service object= stub]
            Naming.rebind("//" +args[0] + "/gumballmachine", gumballMachine);

        } catch (RemoteException e) {
            throw new RuntimeException(e);
        } catch (MalformedURLException e) {
            throw new RuntimeException(e);
        }
    }
}
```

```
실행하기 위해 server 에서 terminal에서 rmiregistry 실행 후 GumballMachinetestDrive.java 파일 실행  
java GumballMachineTestDrive austin.mightgumball.com[host] 100[count]
```


##### GumballMonitor 클라이언트 코드 고치기


```java
package main.designPattern.proxyPattern;

import com.sun.source.tree.LambdaExpressionTree;

import java.rmi.RemoteException;

public class GumballMonitor {
    RemotedGumballMachine machine;

    public GumballMonitor(RemotedGumballMachine machine) {
        this.machine = machine;
    }

//    public void report() {
//        System.out.println("위치 -> ");
//        System.out.println("재고 -> ");
//        System.out.println("현재 상태 -> ");
//    }
    
    
    //클라이언트가 사용할 메소드
    public void report() {
        try {
            System.out.println("뽑기 기계 위치" + machine.getLocation());
            System.out.println("현재 재고" + machine.getCount() + " gumballs");
            System.out.println("현재 상태" + machine.getState());
        } catch (RemoteException e) {
            e.printStackTrace();
        }
    }
}
```


##### 새로운 모니터링 기능 테스트
```
클라이언트가 요청사항 [뽑기 기계를 원격으로 모니터링 할 수 있도록] 하는 코드 테스트
```

```java
package main.designPattern.proxyPattern.rmi;

import main.designPattern.proxyPattern.GumballMonitor;
import main.designPattern.proxyPattern.RemotedGumballMachine;

import java.rmi.Naming;

public class GumballMonitorTestDrive {

    public static void main(String[] args) {
        //monitoring 할 위치
        String[] location = {"rmi//busan.sjgumball.com/gumballmachine",
                             "rmi//seoul.sjgumball.com/gumballmachine",
                             "rmi//daejeon.sjgumball.com/gumballmachine"};

        GumballMonitor[] monitors = new GumballMonitor[location.length];


        for(int i = 0; i < location.length; i++) {
            try {
                //service server 에서 등록된 stub 객체 가져오기
                RemotedGumballMachine machine =
                        (RemotedGumballMachine) Naming.lookup(location[i]);

                //monitoring 할 machine[service object [stub]] 객체를 생성자 인자로 넘겨 생성한다. proxy
                monitors[i] = new GumballMonitor(machine);
                System.out.println(monitors[i]);

            } catch (Exception e) {
                e.printStackTrace();
            }
        }

        for (GumballMonitor monitor : monitors) {
            monitor.report();
        }
    }
}
```

###### 출처: 헤드퍼스트 디자인 패턴: 개정판
