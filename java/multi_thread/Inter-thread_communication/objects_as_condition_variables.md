# objects_as_condition_variables - wait(), notify() and notifyAll()

Object class는 wait(), notify(), notifyAll() method를 갖고있다.
모든 java class는 object 클래스를 상속한다. 그러기에 모든 클래스는 해당 메소드들을 사용할 수 있다.    
모든 객체를 조건 변수 [ condition variable ] 및 lock으로 사용할 수 있습니다(synchronized 키워드 사용).


### wait() - 다른 스레드가 깨울 때까지 현재 스레드를 기다리게 합니다.
- 이 대기 상태에서 스레드는 CPU를 사용하지 않습니다.
- 대기 스레드를 깨울 수 있는 2가지 방법
  - notify() - wakes up a single thread waiting on that object.   [해당 개체를 기다리고 있는 단일 스레드를 깨웁니다. ]
                여러개의 스레드가 wait이라면 그 중 임의의 스레드가 일어나게 된다.
  - notifyAll() - waiting on that object thread들을 깨울 때 사용
- to call wait(), notify(), or notifyAll() we need to acquire the monitor of that object(use synchronized on that object)
 먼저 객체를 동기화 해야한다.


```java
public class MySharedClass {

  private boolean isComplete = false; //일부 작업이 수행되었는지 여부를 나타내는 변수
  //완료될 때 까지 기다리는 메소드
  public void waitUntilComplete() {
    sysnchronized(this) { // 자기 자신을 동기화로 묶음
      while(isComplete == false) {
        this.wait() // 자기 자신을 wait 상태로 만듬 /스레드는 완료될 떄 까지 기다림
      }
    }
  }
}

//동시에 다른 스레드가 task를 끝내고 같은 object의 complete method를 호출한다.
//isComplete = true로 바꾸고,  notify() method를 call하여 첫 번째 스레드를 깨운다.
public void complete() {
  synchronized(this) {
    this.isComplete = true;
    this.notify();
  }
}
```

```java
public class MySharedClass {

  private boolean isComplete = false;
  public void synchronized waitUntilComplete() {
    sysnchronized(this) { // 자기 자신을 동기화로 묶음
      while(isComplete == false) {
        wait() 
      }
    }
  }
}


public void synchronized complete() {
  synchronized(this) {
    isComplete = true;
    notify();
  }
}
```

```text

Object Signalling           | condition Variable

synchronized(obejct) {         lock.lock();
}                              lock.unlock();
object.wait();                 condition.await();
object.notify();               condition.signal();
object.notifyAll()             condition.sinalALl()

```




##### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/13603336#content
