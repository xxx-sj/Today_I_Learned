# observer_pattern [옵저버 패턴]

옵저버 패턴에서는 주제(observable, subject)와 옵저버(observer)가 존재한다.
옵저버 패턴에는 두가지 방식이 있다.   
1.push 방식
  - 주제가 옵저버를 필드로 갖고 있다가 (update(옵저버 메소드)) 상태값 변경 시 옵저버 메소드를 호출하며 변경값을 인자로 넘겨 처리한다.    
  - 모든 데이터가 필요없음에도 모든 데이터를 받을수도있다.

2.pull 방식
  - 주제가 옵저버를 필드로 갖고 있다가 update시 메소드만 인자없이 호출하여 해당 옵저버 객체에서 주제를 필드로 가지며 getter를 통해 해당 필드의 값을 꺼내와 처리한다.


##### 어떠한 값(주제)이 변경되었을 때 해당 값을 갖고 변경이 일어나야하는 객체(옵저버)가 많을경우 사용하면 좋을 듯 하다.

```java
interface Subject { 
  void registerObserver(Observer observer);
  void removeObserver(Observer observer);
  void notifyObserver();
}

interface Observer {
  //void update(...updateObjects);
  void update();
}
```


```java
class ConcreteSubject implements Subject {
    private List<Observer> observers = new ArrayList<>();
    private int a;
    private String b;

    @Override
    public void registerObserver(Observer observer) {
        observers.add(observer);
    }

    @Override
    public void removeObserver(Observer observer) {
        observers.remove(observer);

    }

    @Override
    public void notifyObserver() {
        for (Observer observer : observers) {
        //주제의 데이터가 업데이트 되었다는 것과 업데이트 된 데이터를 전달한다.
            observer.update(a, b);
        }
    }

    //update 데이터 메소드...
    public void updateData(int updateA, String updateB) {
        this.a = updateA;
        this.b = updateB;
        notifyObserver();
    }
}


class ConcreteObserver implements Observer {
    private int a;
    private String b;
    private ConcreteSubject concreteSubject;
    
    public ConcreteObserver(ConcreteSubject concreteSubject) {
      this.concreteSubject = concreteSubject;
    }
    
    @Override
    public void update(int updateA, String updateB) {
        this.a = updateA;
        this.b = updateB;
        //do something
    }
    
    public int getA() {
      return this.a;
    }
    
    public String getB() {
      return this.b;
    }
}
```

```java
class ConcreteSubject implements Subject {
    private List<Observer> observers = new ArrayList<>();
    private int a;
    private String b;

    @Override
    public void registerObserver(Observer observer) {
        observers.add(observer);
    }

    @Override
    public void removeObserver(Observer observer) {
        observers.remove(observer);

    }

    @Override
    public void notifyObserver() {
        for (Observer observer : observers) {
        //주제가 업데이트되었다는 것만 전달한다.
            observer.update();
        }
    }

    //update 데이터 메소드...
    public void updateData(int updateA, String updateB) {
        this.a = updateA;
        this.b = updateB;
        notifyObserver();
    }
}


class ConcreteObserver implements Observer {
    private int a;
    private String b;
    private ConcreteSubject concreteSubject;
    
    public ConcreteObserver(ConcreteSubject concreteSubject) {
      this.concreteSubject = concreteSubject;
    }
    
    @Override
    public void update() {
        this.a = concreteSubject.getA();
        this.b = concreteSubject.getB();
        //do something
    }
}

```



##### 출처: 헤드퍼스트 디자인패턴:개정판
    
