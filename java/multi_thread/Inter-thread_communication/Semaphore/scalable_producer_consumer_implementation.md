# scalable_producer_consumer_implementation

## Introduction

- Can be used to restrict the number of "users" to a particular resource or a group of resources
  [ 특정 리소스 또는 리소스 그룹에 대한 "사용자" 수를 제한하는 데 사용할 수 있습니다. ]

- unlike the locks that allows only one "user" per resource
  [ 리소스당 하나의 "사용자"만 허용하는 잠금과 달리 ]
- The semaphore can restrict any given number of users to a resource
  [ 세마포어는 주어진 수의 사용자를 리소스로 제한할 수 있습니다. ]
 
 이 강의에서는 주차장으로 세마포어를 비유를 했는데, 8개의 차량이 들어갈 수 있는 공간의 주차장에 8개의 차량이 다 차있는 상태에서     
 1대의 차량이 더 들어올 경우 8대의 차량 중 한 차량이 나갈 때 까지 기다리게 되며, 8개의 차량 중 한개의 차량이 나가서 1자리가 빈다면 그것을
 세마포어에게 돌려준다.

```java
Semaphore semaphore = new Semaphore(NUMBER_OF_PERMITS);
semaphore.acquire(); //NUMBER_OF_PERMITS - 1 now available

useResource();
```
처음 세마포어를 초기화 할때 허가 숫자를 인자로 전달하여 초기화하며, 매번 스레드가 acquire method를 호출하여 permit을 획득하여    
다음 명령어로 이동한다. 스레드는 1개 이상의 permit을 얻을 수 있으며, 똑같이 얻은 permit 만큼 release를 해준다.
```java
Semaphore semaphore = new Semaphore(NUMBER_OF_PERMITS);
semaphore.acquire(5); //NUMBER_OF_PERMITS - 5 now available

useResource();

semaphore.release(5);
```

만약 acquire를 호출했지만 남아있는 permit이 없다면 thread는 is blocked된다. 다른 스레드에 의해 semaphore가 해제될 때 까지.    

사실 semaphore은 lock에 대한 좋은 선택은 아니다. semaphore는 lock과 몇 가지가 다르다.
- Semaphore doesn't have a notion of owner thread [ 세마포어에는 소유자 스레드 개념이 없습니다. ]
- many threads can acquire a permit [ 많은 스레드가 허가를 얻을 수 있습니다. ]
- the same thread can acquire the semaphore multiple times [ 동일한 스레드가 세마포어를 여러 번 획득할 수 있습니다. ]  
- the binary semaphore ( initialized with 1) is not reentrant [ 바이너리 세마포어(1로 초기화)는 재진입할 수 없습니다. ]

```java
Semaphore semaphore = new Semaphore(1);

void method() {
  semaphore.acquire(); // 1 now available
  semaphore.acquire(); // thread is blocked
}
```
위와같은 상황에서는 다른 스레드가 세마포어를 릴리즈 해주어야 block된 thread가 깨어난다.     
세마포어를 얻지 않은 스레드도 세마포어를 릴리즈 할 수 있기 때문이다.


- semaphore can be released by any thread [ 모든 스레드에서 세마포어를 해제할 수 있습니다. ]
- even can be released by a thread that hasnt't actually acquired it [ 실제로 획득하지 않은 스레드에 의해 해제될 수도 있습니다. ]

```java

Semaphore sem = new Semaphore(1);

sem.acquire();
useSharedResource();
sem.release();




//

sem.release();
...
sem.acquire();
useSharedResource();
sem.release();
```
위의 예시)
1개의 세마포어를 선언하고 acquire로 얻은 쓰레드가 resource에 접근중인데, 다른 스레드가 접근하여 permit을 release하고, 해당 스레드가 acquire하게    
되면 동시에 2개의 thread가 임계영역에 접근하게 된다. 명백한 버그     
lock에서는 다른 락은 락이 풀린 스레드에만 락을 걸 수 있기 때문에 위와같은 상황이 발생하지 않는다.

세마포어를 사용해야 하는 경우 => 생산자 소비자 [ Producer Consumer ]


## Producet Consumer
```java
Semaphore full = new Semaphore(0); // 초기 full에 acquire하는 thread를 block 되게한다.
Semaphore empty = new Semaphore(1);
Item imte = null; // producer와 consumber에 공유될 자원


Consumber;
while(true) {
  full.acquire();  // is bloacked producer가 item을 produce할 때까지 기다림
  consume(item);
  empty.release();
}

Producer;
while(true) {
  empty.acquire();
  item = produceNewItem();
  full.release();
}
```

처음 세마포어를 0으로 초기화 한다는 것은 처음으로 접근하는 thread를 block한다는 뜻이다.
producer에서 full.release하게 되면 block되어있던 consumber thread가 깨어나, item을 소비하게되고, 다시 producer의     
empty 세마포어를 acquire를 호출하면, consumer에서 release할 때까지 기다리게 된다. 이렇게 되면 빠르고 안전하게    
사용할 수 있다.

item을 queue로 변경하면 많은 producer와 consumer를 쓸 수 있다.
```java
Semaphore full = new Semaphore(0);
Semaphore empty = new Semaphore(CAPACITY); //CAPACITY queue 용량
Queue queue = new ArrayDeque(); // producer와  consumber에 공유될 자원
Lock lock = new ReentrantLock() // 동시 엑세스 하는 것을 보호하기 위한 lock [producer, consumer 둘다. ]


Consumber;
while(true) {
  full.acquire();  // is bloacked producer가 item을 produce할 때까지 기다림
  lock.lock();
  item item = queue.poll();
  lock.unlock();
  consume(item);
  empty.release();
}

Producer;
while(true) {
  Item item = produce();
  empty.acquire();
  lock.lock();
  queue.offer(item);
  lock.unlock();
  full.release();
}
```
소비자와 생산자 여러 개가 큐에 동시에 접근하는 상황을 대비해서 ReentrantLock을 사용한다.    공유변수 queue에 대한 get 하거나, put할 때      
lock을 건다.    
producer는 item을 공유된 변수에 직접적으로 생산하는 것이 아닌, 별도로 제공하고, producer와 consumer에 공유된 queue에 제공한다.     
queue에 offring하기전에 queue를 lock하고 queue에 아이템이 추가되면 lock을 해제한다.
capacity를 사용하여 queue size를 정의하면, queue의 크기가 무한정 늘어나지 않는다는 것을 보장할 수 있다. consumer가 producer를 따라 잡을   
수 있다. [ producer가 생산하는 양을 소비자 consumer가 따라갈 수 있게 한다.  세마포어를 이용했기 때문에 ]

the producer consumer pattern은 다중 스레드 응용 프로그램에서 현대에서 매우 흔한 것이다.
actor model을 사용하는 프레임워크와 스레드 간에 작업 작업을 분산하는 데 사용됩니다.     
tcp 또는 udp 패킷을 소비하고 해당 패킷에 전달하는 소켓 채널용 웹 애플리케이션에서 널리 사용됩니다.
핸들러는 모든 스레드 간 통신 라이브러리 또는 프레임워크를 잘 처리하므로 이 패턴의 버전을 사용하므로    
방금 다룬 사용 사례는 세마포어 사용에 대한 좋은 예일 뿐만 아니라 일반적으로 알아야 할 중요한 개념입니다.

##### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/11200058#content
