# thread termination and daemon threads

### 스레드를 중지하려는 이유와 시기

 스레드가 작업을 수행하지 않는 경우에도 스레드가 리소스를 소비하고 있다. [ 메모리와 일부 커널 리소스 ]     
 스레드가 실행중일 때는 CPU cache memory도 포함하여, CPU시간도 소비하게 된다.    
 - 스레드가 종료되었지만 어플리케이션은 여전히 실행중일 때 우리는 스레드의 리소스를 정리하고 싶습니다.    
 - 스레드를 중지하려는 또 다른 이유는 어떤 식으로든 오작동을 일으키기 때문이다.    
      응답이 없는 서버에 요청을 보내는 것    
      예상했던 것보다 매우 긴 계산에 대하여 
 - 기본적으로 전체 어플리케이션을 종료하고 싶을 때 하나의 thread라도 동작하고 있따면 application은 종료되지 않기 때문이다. 메인 스레드가 멈춤에도 불구하고
 
* * * 
### Thread.interrupt()
thread가 일시 정지 상태에 있을 때 [ thread.sleep(), waiting, timed_waiting, blocked ... ] InterruptedExcpetion을 발생 시키는 역할을 한다.
정지 상태에서 interrupt()를 call하게 되면 error를 발생시키면서 thread는 종료 된다     
interrupt() 메소드는 스레드가 실행 대기 또는 실행 상태에 있을 때는 예외가 발생하지않고, 해당 스레드가 이 후 일시 정지 상태가 되면 interrupt가 발생하게 된다.    

thread.sleep()을 사용할 때 sleep method에서 InterruptedException를 throw하는 것을 확인할 수 있다.

```java
/*
 * MIT License
 *
 * Copyright (c) 2019 Michael Pogrebinsky
 *
 * Permission is hereby granted, free of charge, to any person obtaining a copy
 * of this software and associated documentation files (the "Software"), to deal
 * in the Software without restriction, including without limitation the rights
 * to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
 * copies of the Software, and to permit persons to whom the Software is
 * furnished to do so, subject to the following conditions:
 *
 * The above copyright notice and this permission notice shall be included in all
 * copies or substantial portions of the Software.
 *
 * THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
 * IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
 * FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
 * AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
 * LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
 * OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
 * SOFTWARE.
 */

/**
 * Thread Termination & Daemon Threads
 * https://www.udemy.com/java-multithreading-concurrency-performance-optimization
 */
public class Main1 {
    public static void main(String [] args) {
        Thread thread = new Thread(new BlockingTask());

        thread.start();

        thread.interrupt();
    }

    private static class BlockingTask implements Runnable {

        @Override
        public void run() {
            //do things
            try {
                Thread.sleep(500000);
            } catch (InterruptedException e) {
                System.out.println("Existing blocking thread");
            }
        }
    }
}
```

```java
void main() {
 Thread t = new TestThread();
 t.start();
 t.inturrupt();
}

class TestThreadt {
 void run() {
   for(~; ~ ; ) {
    result.muliply(sumNumber);
   }
 }
}
```
만약 위와 같은 조건에서 for문이 수십만번 돈다고 가정할 때, 해당 for문을 종료시키고 싶어서 inturrupt()를 call해도 종료되지 않는다 
이유는 inturrupt를 처리할 로직이 없기 때문이다. sleep에서는 catch문을 타게 되어 종료되지만, 위와같은 코드에서는 catch와 같은 로직이 없기 때문이다.
만약, 위와 같은 경우가 있다면 먼저 시간이 오래걸리는 스팟을 찾은 후 **Thread.currentThread().isInterrupted()** 를 호출한 후 종료 로직을 넣는다.

```java

for(~ ; ~ ; ~) {
 if(Thread.currentThread().isInterrupted()){
  break;
 }
 //do somthing ...
}
```

* * *

### Daemon Threads
데몬은 사용자가 직접적으로 제어하지 않고, 백그라운드에서 돌면서 여러 작업을 하는 프로그램을 말한다    
백그라운드에서 실행되는 스레드이다. 어플리케이션이 종료되면 해당 스레드도 종료된다.   
데몬스레드를 설정하는 방법은 thread.setDaemon(true) 를 통해 스레드를 데몬으로 만들 수 있다.

```java
public static void main(String[] args) throws InterruptedException {
    Thread thread = new Thread(new LongComputationTask(new BigInteger("200000"), new BigInteger("100000000")));

    thread.setDaemon(true);
    thread.start();
    Thread.sleep(100);
    thread.interrupt();
}
```
위의 예시에서 thread를 새로 생성하고 setDaemon(true)로 데몬스레드라는 것이 알려주었다. 설정한 곳은 main thread이며 
이제 새로 생성한 thread는 daemon thread가 된다.
##### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/11198672#content

##### 참고: https://blog.naver.com/qbxlvnf11/220921178603
