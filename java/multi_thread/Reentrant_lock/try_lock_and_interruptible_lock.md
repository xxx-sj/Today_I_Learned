# tryLock and interruptible lock

## ReenTrantLock

- works just like the synchronized keyword applied on an object [개체에 적용된 동기화된 키워드처럼 작동합니다.]
- Requires explicit locking and unlocking [명백한 lokcing과 unlocking이 필요함]

기존
```java

Object ockObject = new Object();
Resource resource = new Resource();

public void method() {
  synchronized(lockobject) {
    use(resource)
  }
}
```

ReentrantLock 
```java
Lock lockObject = new ReentrantLock();
Resource resource = new Resource();

public void method() {
  lockObject.lock();
  
  use(resource);
  lockObject.unlock();
}
```
Lock interface를 구현한 ReentrantLock을 사용하여 resource에 대해 lock, unlock을 사용하여 보호할 수 있다.

만약에 리소스 사용 후 unlock을 해주지 않으면  lock object는 영원히 lock 상태로 남게 된다. 이러한 경우 버그와 교착 상태[dead lock]의         
원인이 된다.
```java

Lock lockObject = new ReentrantLock();

public void method() {
  lockObject.lock();
  
  //lockObject.unlock();
  return value;
}
```

크리티컬 섹션 내에서 erorr를 던질 경우 unlock 까지 도달하지 못할 수 있다. error가 발생하면 메서드 실행을 그대로 멈추기 때문이다.    
```java
Lock lockObject = new ReentrantLock();

public void method() throws SomeException() {
  lockObject.locK();
  throw new SomeException();
  lockObject.unlock();
}
```
이러한 패턴을 피하기 위해서는 try catch block에서 finally 블록에서 lock을 해제해 준다.
```java
Lock lockObject = new ReentrantLock();

public int method() throws SomeExcpetion() {
  lockObject.lock();
  try {
    someOperation();
    return value;
  }
  finally {
    lockObject.unlock();
  }
}
```

### ReentrantLock을 사용하는 이유
이러한 복잡성에 대해 우리는 많은 보상을 얻는다.
- 더 많은 제어 [lock]
- more lock opertaions

- query methods - for testng
  - getQueuedThreads() -> returns a list of threads waiting to acquire a lock 
      lock을 획득하기 위해 대기 중인 스레드 목록을 반환합니다.
  - getOwner() -> returns the thread theat currently owns the lock
      현재 lock을 소유하고 있는 thread를 반환한다.
  - isHeldByCurrentThread() -> Queries if the lock is held by the current thread
      현재 스레드가 lock을 보유하고 있는지 쿼리합니다.
  - isLocked() -> Queries if the lock is held by any thread at the moment
      스레드가 lock을 보유하고 있는지 쿼리합니다.

- 모든 프로덕션 코드는 철저히 테스트할 필요가 있고,
- isLocked(), getQueuedThreads()그리고 다른 메소드들을 매우 편리하게 사용할 수 있다.
      

ReentrantLock이 빛나는 또 다른 영역은 잠금의 공정성에 대한 제어입니다.

기본적으로 reentrantLock 및 sysnchronized 키워드는 공정성을 보장하지 않습니다.

만약 여러 스레드가 하나의 자원에 대해 접근하려 할 때 thread1이 여러번 resource를 사용한다면, resource를 여러번 사용 한 후 
다른 thread가 resource에 접근이 가능해지는데, 만약 모든 thread에 fainrness를 보장해야 하는 경우, ReentrantLock 생성자에 true 인자를 넘겨    
주어 fiarness하게 만들 수 있다.

그러나, 공정성을 유지하려면 비용이 발생하며 실제로 처리량이 줄어들 수 있다. lock을 취득이 더 오래 걸릴 수 있기 때문에 그러므로 
fairness flag는 꼭 필요한 경우에만 사용한다.
      
      
## ReentrerantLock.lockInterruptibly() 인터럽트 기능

일반적으로 특정 스레드에서 다른 스레드가 이미 있는 동안 lock object를 얻으려고 하면 현재 스레드가 일시 중지되고     
lock이 해제될 때까지 깨어나지 않는다. [ lockObject ]

```java
public class someThread extends Thread {

  public void run() {
    while(true) {
      lockObject.locK();
      
      if(Thread.currentThread().isInterrupted()) {
        cleanUpAndExit()
      }
    }
  }
}
```
lock을 호출한 후 lock이 해제될 때 까지 object는 깨어나지 않는다.
someThread.interrupt()를 호출해도 아무런 반응이 없다.
```java
public class someThread extends Thread {

  public void run() {
    while(true) {
      try {
        lockObject.lockInterruptibly();
      } catch (InterruptedException e ) {
        cleanUpAndExit();
      }
    }
  }
}
```
다른 thread가 이미 lock을 가지고 있어도 lockInterruptibly()를 호출하면 해당 정지상태에서 벗어날 수 있다.
인터럽트를 처리하도록 강제한다. someThread.interrupt()를 호출하면 중단된 thread가 깨어나 catch블록으로 이동한다.

### LockInterruptibly() - use cases
- watchdog for deadlock detection and recovery
- waking up threads to do clean and close the application [ 종료되지 않은 thread들을 interuupted call하여 종료하기 ]


## ReentrantLock.tryLock()
일반적인 lock과 같이 trylock operation은 lock을 획득하며, timeout을 설정하여 lock을 return 할수 있다.
- boolean tryLock();
- boolean tryLock(long timeout, TimeUnit, unit);
  - returns true and acquires a lock if available [true를 반환하고 사용 가능한 경우 잠금을 획득합니다.]
  - returns false and does not get suspended, if the lock is unavailable [잠금을 사용할 수 없는 경우 false를 반환하고 일시 중단되지 않습니다.]


lock
```java
lockObject.lock();
try {
  useResource();
}
finally {
  lockObject.unlock()
}
```
일반적인 lock을 얻는 방법에서는 lock을 가져온 후 다른 스레드가 해당 크리티컬 섹션으로의 접근하는 것을 방지한다.    
안전하게 크리티컬 섹션 내부의 다음 명령으로 간단히 이동하게 된다. 그리고 난 다음 공유 리소스를 사용하여 위협이 완료되면     
자금 해제 메서드를 호출한다.

tryLock
```java
if(lockObject.tryLock()) {
  try {
    useResource()
  }
  fianlly {
    lockObject.lunlock();
  }
} else {...}
```
tryLock 또한 lock을 획득하면서 true를 반환하여 lock이 현재스레드에서 성공적으로 획득되었음을 알려준다.


일반적인 lock 획득방법에서는 thread가 lock을 한번 호출하면, thread는 멀리 이동할 수 없다. lock이 다시 사용가능 해질 때 까지 차단한다.     
현재 lock을 소유한 thread가 unlock한 경우에만 현재 스레드가 깨어나서 lock을 획등하고 다음 명령으로 이동하게 된다.     

tryLock은 이미 다른 thread가 lock을 가지고 있을 경우 해당 메소드는 block 하지 않는다. 대신 tryLock은 즉시 false를 반환하여    
lock이 우리에게 속하지 않으며 공유 리소스를 사용해서는 안된다는 것을 알려준다. 그런다음 thread는 else문에서 일부 resoure가 필요없는    
다른 코드를 실행하기 위해  이동할 수 있다.

Notice
- 어떠 상황에서도 lock 상태에 관계없이 tryLock메서드가 차단되지 않는 다는 점에 유의할 것
- [under no circumstatnces does the tryLock() method block]
- lock 상태와 관계없이 이것은 항상 즉시 반환한다.

lock() 메서드에서 스레드를 일시 중단하는 것이 허용되지 않는 실시간 애플리케이션에서 사용하기 좋음
- ex)
-   video/image processing
-   high speed/low latency trading systems
-   user interface applications




##### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/11200020#content
