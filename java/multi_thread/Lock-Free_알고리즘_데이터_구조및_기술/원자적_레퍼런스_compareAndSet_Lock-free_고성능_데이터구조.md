# 원자적_레퍼런스_compareAndSet_Lock-free_고성능_데이터구조

## AtomicReference<T>
클래스의 객체에 대한 레퍼런스를 감싸줘서 해당 레퍼런스에서 원자적 연산을 수행할 수 있게 해준다.    
AtomicRefernce에 생성자를 전달해서 기존의 객체를 통해 초기화를 하거나 또는 레퍼런스를 나중에 설정할 수 있다.
get(), set()을 호출하여 AtomicReference에서 현재 값을 구하고 또 거기에 새로운 값을 설정할 수 있다.
  
### compareAndSet(V expectedValue, V newValue)
현재값이 기대값과 일치한다는 조건 하에서 새값을 설정하고 현재값이 기대값과 다르면 새 값을 완전히 무시한다.
```java
atmoicRefercence.compareAndSet(oldname, newNmae)
  atomicReference에 할당된 레퍼런스와 oldname의 값이 일치한다면 true를 반환하고 newName으로 값이 변경된다. 
```
  
```java
String oldname = "name";
String newName = "name";
AtomicRefernce<String> atmoicRefercence = new AtomicReference<>(oldname);
  
if (atmoicRefercence.compareAndSet(oldname, newNmae)) {
  system.out.println("new name" + atmoicRefercence.get());
}
```
- compareAndSet은 원자적 클래스에 모두 사용 가능하다 [int, long]
- 이 메서드는 하나의 원자적 하드웨어 연산으로 컴파일되어서 굉장히 강력해진다.

## 락이 걸리지 않는 구조 [Build lock free data structure]

### stack
java가 벡터 클래스를 기반으로 하고 내부적으로 락을 사용하는, 기본값으로 쓸 수 있는 버전의 스택을 지원한다.

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

import java.util.ArrayList;
import java.util.List;
import java.util.Random;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.concurrent.atomic.AtomicReference;
import java.util.concurrent.locks.LockSupport;

/**
 * Atomic References, Compare And Set, Lock-Free High Performance Data Structure
 * https://www.udemy.com/java-multithreading-concurrency-performance-optimization
 */
public class Main {
    public static void main(String[] args) throws InterruptedException {
        //StandardStack<Integer> stack = new StandardStack<>(); //1억5천
        LockFreeStack<Integer> stack = new LockFreeStack<>();// 5억6천  3배 차이.
        Random random = new Random();

        for (int i = 0; i < 100000; i++) {
            stack.push(random.nextInt());
        }

        List<Thread> threads = new ArrayList<>();

        int pushingThreads = 2;
        int poppingThreads = 2;

        for (int i = 0; i < pushingThreads; i++) {
            Thread thread = new Thread(() -> {
                while (true) {
                    stack.push(random.nextInt());
                }
            });

            thread.setDaemon(true);
            threads.add(thread);
        }

        for (int i = 0; i < poppingThreads; i++) {
            Thread thread = new Thread(() -> {
                while (true) {
                    stack.pop();
                }
            });

            thread.setDaemon(true);
            threads.add(thread);
        }

        for (Thread thread : threads) {
            thread.start();
        }

        //10초 동안 push and pop 하고
        Thread.sleep(10000);

        //얼마나 많은 수행이 있었는지 counter를 print 한다.
        System.out.println(String.format("%,d operations were performed in 10 seconds ", stack.getCounter()));
    }

    /**
     * push, pop: 헤드의 현재값을 읽은 다음 해당 값을 기반으로 헤등를 대체할
     * 새 후보자 값을 계산하는데 그러면서 방금 읽은 값이 변하지 않았기를 기원하는 것.
     * 여기서 compareAndSet 메서드를 써서 예상값이 여전히 유요한지 확인하고
     * 헤드의 값을 그 새 후보자로 설정하는 것이다.
     * 만약 변하였다면, false를 반환하고 성공할때 까지 반복하는 것이다. [while]
     *
     */
    public static class LockFreeStack<T> {
        //atomicReference
        private AtomicReference<StackNode<T>> head = new AtomicReference<>();
        private AtomicInteger counter = new AtomicInteger(0);

        public void push(T value) {
            StackNode<T> newHeadNode = new StackNode<>(value);

            //반복적으로 생성하는 연산
            /**
             * loop가 필요한 이유는 많은 스레드가 동시에 스택으로 항목을
             * 푸시하거나 또는 스택에서 팝하려고 할 거라서 성공하려면 몇 번 정도
             * 시도를 해야하기 때문이다.
             */
            while (true) {
                //원자적 레퍼런스인 헤드가 그 시점에 가리키고 있는 currentHeadNode
                //stack에 newHead 넣기전.
                StackNode<T> currentHeadNode = head.get();
                //새로운 newHead의 next에 현재 head값을 next에 저장.
                newHeadNode.next = currentHeadNode;
                //원자적 레퍼런스인 헤드는 현재 currentHeadNode를 가리키고 있음
                if (head.compareAndSet(currentHeadNode, newHeadNode)) {
                    //condition이 true라면 head를 newHead 값으로 변경하고 while을 빠져나간다.
                    //true라는 것은 어떠한 thread에 의해서 값이 변경되지 않았다는 것이다.
                    break;
                } else {
                    //false를 반환하면 헤드를 읽는 연산[heae]과 newHeadNode로 헤드를
                    //업데이트 하는 연산[head.compareAndSet(currentHeadNode, newHeadNode)]
                    // 사이에 헤드가 변경되었다는 뜻으로 그 모든 연산을 다시 수행해야 하는데
                    // 다시 시도하기 전에 1ns 기다리고 다시 진행한다.
                    LockSupport.parkNanos(1);
                }
            }
            //원자적integer로 선언하여 counter를 증가시키는 구문을 원자적연산으로 변경
            counter.incrementAndGet();
        }

        public T pop() {
            //pop하려는 현재 노드를 get으로 가져온다.
            StackNode<T> currentHeadNode = head.get();
            //pop되고 나서의 새로운 head 노드를 나타낼 값.
            StackNode<T> newHeadNode;

            //while loop는 현재 헤드가 null이 아닐때만 돌아간다.
            while (currentHeadNode != null) {
                //현재 노드의 다음값을 새로운 헤드값으로 할당하고
                newHeadNode = currentHeadNode.next;
                //헤드의 값이 get()으로 얻은 현재 헤드 노드 값과 일치한다면
                if (head.compareAndSet(currentHeadNode, newHeadNode)) {
                    //newHeadNode로 head의 값을 변경하고 break; 한다.
                    break;
                } else {
                    //false라면 head의 값과 currentHeadNode snapshot의 값이
                    //일치 하지 않다는 것으로 다른 스레드에 의해 값이 변경되었다는 뜻으로
                    //1ns 기다린후 head에서 get을 통해 현재 헤드노드의 snapshot 값을 변경한다.
                    LockSupport.parkNanos(1);
                    currentHeadNode = head.get();
                }
            }
            //counter의 원자적 연산
            counter.incrementAndGet();
            //pop의 return으로 현재 노드가 null이 아니라면 value값을 null이라면 null 반환.
            return currentHeadNode != null ? currentHeadNode.value : null;
        }

        public int getCounter() {
            return counter.get();
        }
    }

    //동기화를 쓰지 않으면 여러 경쟁 상태가 가능해진다.
    public static class StandardStack<T> {
        private StackNode<T> head;
        private int counter = 0;

        public synchronized void push(T value) {
            //새로운 head를 생성 가장 위에 쌓일 값.
            StackNode<T> newHead = new StackNode<>(value);
            //=================race condition======================
            //newhead의 다음 값으로 이전 헤드값 저장
            newHead.next = head;
            //헤드에 newHead [새로운 헤드] 값 저장
            head = newHead;
            //=======================================
            //counter 증가
            counter++;
        }

        public synchronized T pop() {
            //head가 null 이라면.
            if (head == null) {
                //counter만 증가 시키고 null 반환.
                counter++;
                return null;
            }
            //===============race condition========================
            //null이 아니라면 head의 값을 스냅샷 찍어서
            T value = head.value;
            //헤드의 다음값을 헤드로 변경 [한칸을 내린다.]
            head = head.next;
            //카운터 증가시키고 value 반환.
            //=======================================
            counter++;
            return value;
        }

        public int getCounter() {
            return counter;
        }
    }

    //연결 리스트 value node에 해당하는 값
    private static class StackNode<T> {
        public T value;
        public StackNode<T> next;

        public StackNode(T value) {
            this.value = value;
            this.next = next; //현재 노드의 다음 값. stack의 node 아래 값.
        }
    }
}

```
##### 출처: https://www.udemy.com/course/java-multi-threading/learn/lecture/30800378#content
