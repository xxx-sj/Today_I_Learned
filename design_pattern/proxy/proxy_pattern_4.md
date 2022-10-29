# 프록시 패턴을 이용한 데이팅 서비스 샘플

```
서로 상대방의 호감 지수)를 매기는 기능을 더해서 사용자들이 더 적극적으로 서비스를 이용하고  더좋은 상대를 찾을 수 있도록 
만들기로 했다.

이 서비스는 어떤 사람의 정보를 설정하거나 가져올 수 있는 Person 인터페이스를 중심으로 돌아간다.
```


###### Person interface

```java
public interface Person {

    String getName();
    String getGender();
    String getInterests(); //관심사항
    int getGeekRating();

    void setName(String name);
    void setGender(String gender);
    void setInterests(String interests);
    void setGeekRating(int rating);

}

```

```
스스로가 점수를매긴다거나, 다른 고객의 정보를 마음대로 바꾸는 행위를 막아야 한다.
```

### 보호 프록시란?
```
보호 프록시는 접근 권한을 바탕으로 객체로의 접근을 제어하는 프록시 예를 들어,     
회사 직원을 나타내는 객체가 있다면 일반 직원 객체에서 호출할 수 있는 메소드가 정해져 있고,  
관리자 객체는 (월급을 설정하는 setSalary()를 비롯한) 더 많은 메소드를 호출할 수 있고, 인사과 직원 객체는 모든 메소드를 
호출할 수 있도록 만들어야 한다.
```

```
자기 호감 지수는 직접 조작할 수없어야 하고, 다른 사람의 개인정보도 수정할 수 없도록 해야한다.
```
1. 2개의 InvocationHandler 만들기
```
InvocationHandler는 프록시의 행동을 구현한다. 프록시 클래스와 객체를 만드는 일은 자바에서 해주기 때문에 프록시의 메소드가    
호출되었을 때 할 일을 지정해 주는 핸들러만 만들면 된다.
```
2. 동적 프록시 생성 코드 만들기
```
프록시 클래스를 생성하고 그 인스턴스를 만드는 코드가 필요하다.
```
3. 적절한 프록시로 Person 객체 감싸기
```
Person 객체를 사용하는 객체는 고객 자신의 객체(owner), 아니면 데이팅 서비스를 사용하는 다른 고객의 객체(non-owner)
이렇게 둘 중 하나입니다. 어떤 경우든 해당 Person에 따라서 적절한 프록시를 생성해야한다.
```




#### 1.InvocationHandler 만들기
```
2개의 호출 핸들러를 만들어야 한다. 하나는 owner 핸들러 다른 하나는 non-owner 핸들러
호출핸들러란?

프록시의 메소드가 호출되면 프록시는 그 호출을 호출 핸들러에게 넘긴다. 하지만 호출 핸들러에 있는 같은 이름의 메소드를 호출하는 것은 아니다.
프록시의 어떤 메소드가 호출되는 무조건 핸들러에 있는 invoke() 메소드가 호출된다.
```


```java
proxy.setGeekRating(9)
proxy -> proxy 인자로, setGeekRating -> method 인자로, 9 -> args 인자로
invoke(Object proxy, Method method, Object[] args)

//리플렉션 api에 있는 method클래스의 getName() 메소드로 프록시의 어떤 메소드가 호출되었는지 알 수 있다.
```

1. 프록시의 setGeekRating()메소드가 호출되었다.
2. 프록시는 InvocationHandler의 invoke() 메소드를 호출한다.
3. 핸들러에서는 주어진 요청을 어떻게 처리할지 결정한 다음 상황에 따라 RealSubject에 그 요청을 전달할 수 있다.
   핸들러에서 결정하는 방법은?

```java
//RealSubject의 메소드를 호출하는 방법
return method.invoke(person, args)
//method -> 프록시에 호출되었던 진짜 메소드를 호출한다[setGeekRating]. method는 invoke() 메소드 호출로 전달된 객체이다.
//invoke 인자로 실제 객체에 메소드를 호출하기위한 person과 args[처음에 인자로 받았던]를 넘긴다.
```

#### 프록시가 invoke()를 호출했을 때 그 호출을 처리하는 방법을 어떻게 알 수 있을까?

```
일반적으로는 프록시에서 호출된 메소드를 확인해 보고 메소드의 이름과 인자를 바탕으로 결정한다.
본인이 호출할 때 사용되는 OwnerInvocationHandler를 직접 구현해 보자.
```

```java

//1                                           //2
public class OwnerInvocationHandler implements InvocationHandler {

    Person person;


      //3
    public OwnerInvocationHandler(Person person) {
        this.person = person;
    }


    @Override   //4
    public Object invoke(Object proxy, Method method, Object[] args) throws IllegalAccessException {

        try {
            if (method.getName().startsWith("get")) { //5
                return method.invoke(person, args);
            } else if (method.getName().equals("setGeekRating")) { //6
                return new IllegalAccessException();
            } else if (method.getName().startsWith("set")) { //7
                return method.invoke(person, args);
            }
        } catch (InvocationTargetException e) { //8
            e.printStackTrace();
        }

        return null; //9
    }
}
```


```
1. 자기 자신 호출 핸들러
2. 호출 핸들러는 invocationHandler 인터페이스를 구현한다.
3. 생성자로 전달받은 주제의 레퍼런스를 저장한다.
4. 프록시의 메소드가 호출될 때마다 호출되는 invoke 메소드
5. 게터 메소드라면 주제의 메소드를 호출한다 [모두 접근 가능]
6. setGeekRating 접근 시 오류 발생 (스스로에게 점수를 주면 안되기 때문에)
7. set메소드 호출 시 허용 [자기 자신 정보 수정]
8. 주제에서 예외를 던졌을 때 처리
9. 다른 메소드가 호출된다면 null을 리턴한다.
```


### 동적 프록시 생성 코드 만들기
```
동적으로 프록시 클래스를 생성하는 프록시 객체 인스턴스를 만들어야한다.   
먼저 Person 객체를 인자로 받고 본인용 프록시를 만드는 메소드를 만든다.    
메소드 호출을 OwnerIncationHandler에게 넘겨주는 프록시를 만든다.
```

```java
Person getOwnerProxy(Person person) {

        return (Person) Proxy.newProxyInstance(
                person.getClass().getClassLoader(),
                person.getClass().getInterfaces(),
                new OwnerInvocationHandler(person));
    }
```

```
위 메소드는 Person 객체(진짜 객체)를 인자로 받고 프록시를 리턴한다. 프록시의 인터페이스는 주제의 인터페이스와    
똑같으므로 리턴 형식은 Person이 된다. [proxy와 person 는 같은 인터페이스를 사용한다.]
1. proxy 클래스에 있는 정적메소드를 사용하여 프록시를 생성한다.
2. person의 클래스로더를 인자로 전달한다.
3. 프록에서 구현해야 하는 인터페이스도 인자로 전달한다. [person interface - method들이 있는 곳]
4. 호출 핸들러인 OwnerInvocationHandler도 인자로 전달한다.

해당 프록시에 들어온 요청은 OwnerInvocationHandler 의 invoke로 전달되며, 프록시 생성 시 인자로 넘겼던 Person(진짜 객체)을 가지고 invoke 메소드    
내부를 실행한다.
```


```java
public class MatchMakingTestDrive {

    //some

    public static void main(String[] args) {
        MatchMakingTestDrive test = new MatchMakingTestDrive();
        test.drive();
    }

    public MatchMakingTestDrive() {
        //서비스의 회원 데이터베이스를 초기화 한다.
        initializeDatabase();
    }

    public void drive() {
        Person p = getPserOnFromDatabase("p"); //인물정보를 데이터베이스에서 가져온다.
        Person ownerProxy = getOwnerProxy(kim); //본인용 프록시를 생성한다.
        System.out.println("이름: " + ownerProxy.getName());
        ownerProxy.setInterests("탁구");
        System.out.println("본인 프록시에 관심사항을 등록한다.");
        try {
            ownerProxy.setGeekRating(10);
        } catch (Exception e) {
            System.out.println("본인 프록시에 지수를 매길 수 없다.");
        }

        Person nonOwnerProxy = getNonOwnerProxy(p); //타인 프록시를 생성한다.
        System.out.println("이름: " + nonOwnerProxy.getName());
        try {
            nonOwnerProxy.setInterests("탁구"); //타인 흥미 수정 불가능
        } catch (Exception e) {
            System.out.println("타인 프록시에 관심 사항을 등록할 수 없다.");
        }
        nonOwnerProxy.setGeekRating(3);
        System.out.println("타인에게 점수를 매깁니다.");
        System.out.println("지수 : " + nonOwnerProxy.getGender());
    }



    Person getOwnerProxy(Person person) {

        return (Person) Proxy.newProxyInstance(
                person.getClass().getClassLoader(),
                person.getClass().getInterfaces(),
                new OwnerInvocationHandler(person));
    }
}

```


### 여러가지 프록시

- 방화벽 프록시
  일련의 네트워크 자원으로의 접근을 제어함으로써 주제를 "bad" client 로 부터 보호한다. :: 기업용 방화벽 시스템

- 스마트 레퍼런스 프록시
  주제가 참조될 때마다 추가 행동을 제공한다. 객체의 레퍼런스 개수를 센다하는 식으로..

- 캐싱 프록시 :: 웹 서버 프록시 또는 컨텐츠 관리 및 퍼블리싱 시스템
  비용이 많이 드는 작업의 결과를 임시로 저장해 준다. 여러 클라이언트에서 결과를 공유학게 해 줌으로써  
  계산 시간과 네트워크 지연을 줄여주는 효과도 있다.
  
- 동기화 프록시 :: 분산환경에서 일련의 객체로의 동기화된 접근을 제어해 주는 자바 스페이스라는 곳에서
  여러 스레드에서 주제에 접글할 때 안전하게 처리할 수 있게 해준다.

- 복잡도 숨김 프록시(complexity hiding proxy)
  복잡한 클래스의 집합으로의 접근을 제어하고, 그 복잡도를 숨겨준다. 퍼사드 프록시라고 부르기도 한다.
  이 프록시와 퍼사드 패턴의 차이점은 프록시는 접근을 제어하지만, 퍼사드 패턴은 대체 인터페이스만 제공한다는 점

- 지연 복사 프록시(copy-on-write proxy)::자바의 copyOnWriteArrayList
  클라이언트에서 필요로 할 때까지 객체가 복사되는 것을 지연시킴으로써 객체의 복사를 제어한다. 
  변형된 가상 프록시라고 할 수 있다.


##### 출처: 헤드퍼스트 디자인패턴: 개정판
