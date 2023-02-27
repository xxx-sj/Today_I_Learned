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

**wait(), notify() or notifyAll() 을 호출할 때 주의할 건 먼저 객체를 동기화 해야 한다는 것이다.**

```java
//클래스가 있고 여러 스레드가 클래스 객체를 공유하고 있다.
public class MySharedClass {

  private boolean isComplete = false; //작업의 완료 상태를 알려주는 변수
  
  //완료될 때 까지 기다리는 메소드
  public void waitUntilComplete() {
    sysnchronized(this) { // 자기 자신을 동기화로 묶음
      while(isComplete == false) { //complete 변수가 true인지 확인
        this.wait() // 참이 아니면 스레드는 wait 상태가 되고 cpu를 완전히 포기해 버린다.
      }
    }
  }
}

//병렬적으로 다른 스레드가 작업을 완료할 수 있고 같은 객체 [class]에 complete 메서드를 호출할 수 있다.
//isComplete = true로 바꾸고,  notify() method를 call하여 첫 번째 스레드를 깨운다.
public void complete() {
  synchronized(this) {
    this.isComplete = true; // isComplete 변수를 참으로 변경하고 나서
    this.notify(); // 첫 스레드를 깨울 notify를 call한다.
  }
}
```

어떤 객체라도 사용할 수 있지만 현재 객체를 사용하면 메서드 선언에 synchronized 키워드를 옮기고 암시적인 wait 메서드    
앞의 키워드를 제거해 코드를 정리할 수 있다.

```java
public class MySharedClass {

  private boolean isComplete = false;
  public void synchronized waitUntilComplete() {
      while(isComplete == false) {
        wait() 
      }
    }
  }
}


public void synchronized complete() {
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
조건 변수와 매우 비슷하지만, 현재 객체를 lock 그리고 조건 변수로 사용하고 있다.



```
예제 코드 참조 wait-notify-example
```

계속 증가하게 된다면, 생산자가 생산하는 양을 소비자가 따라가지 못하여 메모르 부족에러가 날 수 있다.    
입력 스트림이 큰 파일에서 들어온다면 예를들어 네트워크 스트림.,
이것을 방지하기위해 생산자에 백프레셔를 적용할 방법이 필요하다.
큐에 최적 용량을 정한다. capacity = 5



##### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/13603336#content
