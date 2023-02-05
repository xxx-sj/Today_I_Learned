# the concurrency challenges and solutions

일련의 작업을 수행할 때 enter critical section과 exit critical section으로 task들을 묶어서 하나의 thread가 작업 중일 때 다른 thread가    
작업을 방해하지 못하게 막아야 한다.

java에서 os의 도움을 받는 jvm과 하드웨가 critical section을 위한 여러 도구를 제공한다.    

### synchronized keyword
- critical section에 restict 한 접근을 사용하거나, 동시에 싱글 스레드만이 메소드에 접근 가능하도록 방지하도록 설계된 locking mechanism 이다.    
- synchroized keyword를 사용하는 2가지 방법이 있다.

1. class에서 하나 이상의 메소드에 synchronized를 선언하는 것이다.
    ```java
    public synchronized method() {}
    public synchronized method2() {}
    ```
    이 클래스의 경우 하나의 스레드만 이러한 메소드 중 하나를 실행할 수 있다.    
    여기서 주의해야 할 점은 하나의 thread가 method를 실행중에 있다면 다른 thread는 method와 method2의 실행에서 제외된다.
    이유는 synchronized가 object당 적용되기 때문이다. 이것을 monitort라 부른다.    
    하나의 문이 닫히면 모든 다른 문도 즉시 그리고 자동으로 잠긴다.     
    
2. synchronized keyword를 사용하는 두 번째 방법은 method 전체를 synchronized 하지않고 synchronized 블록을 정의하는 것이다.
  사용하기 위해서는 먼저 동기화할 객체를 생성해야 한다. 어떠한 object도 가능하다. 생성된 블럭 내에는 하나의 스레드만의 접근을 허용한다.
  이 전 1에서 사용했던 method name에 synchronized를 붙이는 것과 동일하게
  ```java
  public void method() {
  synchronized(this) {}
  }
  ```
  와 같이 사용하면 동일하다. 장점으로는    
  ```java
  public void method() {
  synchronized(A) {}
  }
  public void method2() {
  synchronized(B) {}
  }
  ```
  와 같이 정의하게 되면 method와 method2에 서로 다른 스레드가 접근이 가능해진다. 만약, method에 하나의 스레드가 task를 진행중에 있다면
  이 역시,다른 스레드는 method의 task가 끝날 때 까지 대기하게된다.     
  다른 이점으로는 method 전체를 synchronized 하지 않아도 된다.     
  concurrent execution      
  non-concurrent execution    
  concurrent execution      
  와 같이 동기화 부분 코드를 최소화 할 수 있다. 이러한 전략이 application에서 좋은 성능을 얻을 수 있다.
  
  ```java
  class a {
  
  Object lock = new Object();
  
    public void method() {
      synchronized (this.lock) {
        // do something..
      }
    }
    public void method2() {
    synchronized (this.lock) {
      // do something..
    }
  }
  }
  ```
하나의 스레드가 synchronized 인 method를 실행중에 있다면 다른 synchronized method 또한 block 내에서 call할 수 있다.
```java
    public void method() {
      synchronized (this.lock) {
        // do something..
        method2();    //
      }
    }
    public void method2() {
    synchronized (this.lock) {
      // do something..
    }
  }
```

##### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/11200008#content
