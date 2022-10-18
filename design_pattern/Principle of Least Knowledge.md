# Principle of Least Knowledge [최소 지식 원칙]

```
객체 사이의 상호작용은 될 수 있으면 아주 가까운 '친구'사이에서만 허용하는 편이 좋다.
시스템을 디자인할 때 어떤 객체든 그 객체와 상호작용을 하는 클래스의 개수와 상호작용 방식에
주의를 기울여야 한다는 뜻이다.
복잡하게 얽혀 있어서, 시스템의 한 부분을 변경했을 때 다른 부분까지 줄줄이 고쳐야 하는 상황을
미리 방지할 수 있다.
```


- 객체 자체
- 메소드에 매개변수로 전달된 객체
- 메소드를 생성하거나 인스턴스를 만든 객체
- 객체에 속하는 구성 요소
  - '구성 요소'는 인스턴스 변수에 의해 참조되는 객체를 의미한다
  - A에는 B가 있다 라는 관계(has a)에 있는 객체를 생각하면 된다.  


- 다른 메소드를 호출해서 리턴받은 객체의 메소드를 호출하는 일도 바람직하지 않다.

```java
class testClass {
   
    
    //xxx
    public float getTemp() {
        //station으로부터 객체를 받은다음 그 객체의 getTemperature 메소드를 호출함 xx
        Thermometer thermometer = station.getThermometer();
        return thermometer.getTmperature();
    }
    
    //ooo
    public float getTemp2() {
        //thermometer에게 요청을 전달하는 메소드를 station 클래스에 추가한다.
        return station.getTemperature();
    }
}

```


```java

class Car {
    //구성요소
    //이 구성 요소의 메소드는 호출해도 된다.
    Engine engine;
    
    public Car() {
        //초기화
    }
    
    /*
    매개변수로 전달된(key)의 메소드는 호출해도 된다.
    새로운 객체(doors)를 생성한다. 이객체의 메소드는 호출해도된다.
    구성요소의 메소드 호출 가능
    객체 내에 있는 메소드 호출 가능
    직접 생성한 인스턴스 메소드 호출 가능
     */
    public void  start(Key key) {
        Doors doors = new Doors();
        boolean authorized = key.turns();
        if (authorized) {
            engine.start();
            updateDashboardDisplay();
            doors.lock();
        }
    }
    
    public void updateDashboardDisplay() {
        //update display
    }
}

```
##### 넘겨받은객체, 내부에서 생성한 객체, 내부 필드 객체, 내부 메소드...

```
클라이언트는 hometheater 하나뿐이다. 객체지향에서는 친구가 하나만 있는것이 좋다.
hometheater는 클라이언트 대신 모든 서브시스템 구성 요소를 관리해 준다.
덕분에 클라이언트는 단순하면서도 유연해 질 수 있다.
홈시어터 구성요소를 업그레이드(추가)해도 클라이언트는 영향을 받지 않는다.

서브시스템에도 최소 지식 원칙을 최대한 지키는것이 좋다.
서로 얽혀 있는 친구가 너무 많아 시스템이 복잡하다면 퍼사드를 추가하는 것도
생각해 볼만 하다.
```



##### 출처: 헤드퍼스트 디자인패턴: 개정판
