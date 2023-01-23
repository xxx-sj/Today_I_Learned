# threads creation

### 스레드 생성하는 법

java에서 thread를 생성할 때는 java.lang package 내부에 있는 Thread를 사용하여 생성한다. 
thread 생성 시 생성자에 인자로 new Runnable() interface를 구현한 객체를 넘긴다. [ 대부분 익명 클래스로 넘길때가 많다.]

```java
public class Main {
  public static void main(String[] args) throws InterruptedException {
      Thread thread = new Thread(new Runnable() {
          @Override
          public void run() {
              //Code that will run in a new thread
              System.out.println("We are in thread" + Thread.currentThread().getName());
          }
      });

      /**
       * Thread.currentThread(): 현재 스레드를 가져온다
       *      .getName() : 현재 스레드의 이름
       */
      System.out.println("WE are in thread: " + Thread.currentThread().getName() + "before starting a new thread");
      /**
       * start를 call하면 jvm에게 새 스레드를 생성하고
       * 운영 제체에 전달하도록 지시한다.
       */
      thread.start();

      System.out.println("WE are in thread: " + Thread.currentThread().getName() + "after starting a new thread");

      /**
       * 현재 실행중인 thread를 10초간 sleep 한다.
       * os에게 값으로 넘긴 millis 만큼 현재 스레드를 스케쥴하지 않는다.
       * 그 시간동안 thread는 cpu를 사용하지 않게 된다.
       */
      Thread.sleep(10000);
  }
}
```

이 후 코드를 실행해 보면     
```js
We are in thread: main before starting a new thread
We are in thread: main after starting a new thread
we are now in thread New Worker Thread
Current thread priority is 10
```
이러한 결과를 얻게 된다. print method를 main에서 실행했기 때문에 thread name도 main으로 나온것이고,
start() method로 새로운 thread를 실행한 결과보다 after가 먼저 나온 이유는 아직 스레드가 scheduled [예약] 되지 않은 상태이고
시간이 좀 걸리며, os에 의해 synchronously 으로 발생하기 때문이다.

##### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/11198608#content
