# Locking Strateiges and deadlocks

## >> deadlock
두 개의 스레드가 공유된 자원을 서로 사용하려다 발생하는 교착상태를 말한다.    
스레드A가 A 리소스에 대해 lock을 걸고, 또 다른 스레드B는 B 리소스에 대해 lock을 걸어놓은 상태에서, A스레드가 B 스레드의 자원을 사용하려 한다면, 현재 리소스B에 대하여
스레드B가 점유하고 있기 대문에 대기를 하게 된다. 이 상태에서 다시 스레드B가 리소스A를 사용하려 할 때 스레드A가 해당 자원을 소유하고 있어 해당 작업이 끝나기를 기다리게 된다.
서로의 스레드가 작업이 끝나기를 기다리게 되면서 교착상태 [dead lock]에 빠지게 된다.

Thread[A]    
lock(A)     
lock(B)    
delete(A, item)    
add(B, item)    
unlock(B)    
unlock(A)    

Thread [B]    
lock(B)    
lcok(A)    
delete(B, item)    
add(A, item)    
unlock(A)    
unlock(B)        

스케줄 되어 ThreeadA가 lock(A)를 하고, 다시 threadB가 스케줄되어 lock(B)를 하게 되고 이후 lock(A)를 하려할 때 threadA의 resourceA에 대한 점유가 끝나길 기다리게 되고, 
다시 threadA가 스케줄되어 B의 자원을 사용하려 할 때 threadB의 점유가 끝나길 기다리게되면서 서로 기다리는 상태가 되어 dead lock이 발생하게 된다.


```java

public class Main {

pubic static void main() {
  Intersection intersection = new Intersection();
  Thread threadA = new Thread(new TrainA(intersection));
  Thread threadB = new Thread(new TrainB(intersection));
  
  threadA.start();
  threadB.start();
}


public static class TrainA implements Runnable {
  private Intersection intersection;
  private Random random = new Randon();
  
  public TrainA(Intersection intersection) {
    this.intersection = intersection; 
  }
  
  @Override
  public void run() {
    while(true) {
      long sleepingTime = random.nextint(5);
      
      try {
        slepp(sleepingTime);
      } catch (InterruptException e) {}
      
      this.intersection.takeRoadA();
    } 
  }
}

public static class TrainB implements Runnable {
  private Intersection intersection;
  private Random random = new Randon();
  
  public TrainB(Intersection intersection) {
    this.intersection = intersection; 
  }
  
  @Override
  public void run() {
    while(true) {
      long sleepingTime = random.nextint(5);
      
      try {
        slepp(sleepingTime);
      } catch (InterruptException e) {}
      
      this.intersection.takeRoadB();
    } 
  }
}

  public static class Intersection {
    private Object roadA = new Object();
    private Object roadB = new Object();

    public void takeRoadA() {
      synchronized(roadA) {
        println();

        synchronized(roadB) {
          println()
          try {
            sleep(1);
          } catch (InterruptedException e ) {}
        }
      }
    }

    public void takeRoadB() {
      synchronized(roadB) {
        println();

        synchronized(roadA) {
          println()
          try {
            sleep(1);
          } catch (InterruptedException e ) {}
        }
      }
    }
  } 
  
}
```

### deadlcok의 조건
- 상홉 배제 [ mutual exclusion ]
    하나의 스레드만 하나의 자원에 독점적인 엑세스를 할 수 있다. [ 여러 자원이 동시에 하나의 자원을 사용할 수 없다. ]
    위의 예시에서 하나의 road는 하나의 train만 사용할 수 있다. 
- holding and wait
  적어도 하나의 스레드가 하나의 리소르를 잡고있고 또 다른 리소스를 기다리고 있을 때.   
  lock(A), lock(B)
- 비 선점 할당 [ non-preemptive allocation ]
  하나의 리소스는 해당 스레드가 리소스를 사용한 후에만 해제 된다.
  다시 말해 다른 스레드의 리소르를 어떠한 스레드도 가져갈 수 없다. 해당 리소스가 사용한 후 해제돌때까지 기다리게 된다.
- 순환 대기 [ circular wait ]
  각각 하나의 리소스를 보유하고 다른 리소스를 기다리는 두 개 이상의 스레드 체인


### solution
- 순홛 대기 피하기 [ avoid circular wait ]
  lock 획득 시 엄격한 순서 적용
  이 전에 보았던 lock을 통해 해당 resource를 점유하는 순서를 변경한다. ThreadA에서 lock(A), lock(B) 순서 였다면 threadB에서도 lock(B), lock(A)가     
  아닌 lock(A), lock(B) 순서로 점유하게 만든다. 스레드A가 변경되어도 된다. unlcok 순서는 여기서 중요하지 않다..??

만약 다른 장소에서 많은 lock들이 사용될 경우 [ 위 solution을 적용하기 어려울 때 ]
- deadlockdetection - watchdog
- thread interruption (not possible with synchronized )
- tryLock operations (not possible with synchronized )



##### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/11200018#content

