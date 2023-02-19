# ReentrantReadWritelock

ReentrantReadWriteLock은 실제 하나에 읽기와 쓰기 lock 2개가 있는 것이다.

Race conditions require [ 경쟁상태 필요사항 ]
- Multiple threads sharing a reosurce
- at least one thread modifying the resource
- 해결방법 complete mutual exlusion [완전한 상호 배제]
  - regardless of operation (read/write/both)
  - Lock and allow only one thread to critical section[임계 영역]   



일반적으로 data cache가 있을 때 reader는 빈번하게 일어나며, writer는 reader에 비해 덜 자주 사용하게 될텐데, 
이런 reader와 writer 사이의 경쟁상태로부터 보호하기 위해 lock이 필요하다.

여러개의 reader 사이에 보호가 필요한가? 대답은 no 이다. 여러 스레드가 동시에 공유 리소스에서 안전하게 읽을수 있다. 상태를 수정하지 [writer]      
경우를 제외

### use case
- synchronized and reentrantLcok do not allow multiple readers to access a shared resource concurrently
  [synchronized 및 reentrantLcok는 여러 reader가 공유 리소스에 동시에 액세스하는 것을 허용하지 않습니다.]
- 일반적인 경우 이것은 큰 문제가 아니다. [not a big problem in the general case]
  읽기 작업은 일반적으로 빠르며 
- if we keep the critical sections short, the chances of a contention over a lock are minimal
  [ 크리티컬 섹션을 짧게 유지하면 lock에 대한 경쟁 가능성이 최소화됩니다. ]

### when to use
- when read operations are predominant [ 읽기 작업이 우세한 경우 ]
- or when the read operations are not as fast [ 또는 읽기 작업이 빠르지 않을 때 ]
  - read from many variables
  - read from a complex data structure
- mutual exclusion of reading threads negatively impacts the performance
    [ 읽기 스레드의 상호 배제는 성능에 부정적인 영향을 미칩니다. ]
    
    
    
```java
ReentrantReadWriteLock rwLock = new ReentrantReadWriteLock();
Lock readLock = rwLock.readLock();
Lock writeLock = rwLock.writeLock();

writeLock.lock();
try {
  modifySharedResources();
} finally {
  writeLock.unlock();
}

readLock.lock();
try {
  readFromSharedResources();
} finally {
  readLock.unlock();
}
```
write lock은 공유 리소스를 수정할 때 사용한다. read lock은 read할 때 사용.     

- Multiple threads can acquire the readLock
- 여러 스레드가 동시에 read lock을 획득할 수 있다 즉, 임계영역에 여러 스레드가 동시에 접근이 가능하다.
- readLock은 내부적으로 몇개의 reader thread가 lock을 획득했는지 count 한다.

write lock은 오직 한개의 스레드만 접근이 가능하다.


마지막으로 reader와 writer 사이에 상호 배제[mutual exclusion] 이 있다. 
 - if a write lock is acquired no thread can acquire a read lock
    [writer lock 획득되면 어떤 스레드도 read lock을  획득할 수 없습니다.]
- if at least one thread holds a read lock, no thread can acquire a write lock
    [ 하나 이상의 스레드가 읽기 잠금을 보유하고 있으면 어떤 스레드도 쓰기 잠금을 획득할 수 없습니다. ]


## Pracical use case

udemy 코드 참고 주석 달아놓음

약 reader thread 실행 시간 체크시 3초 [reentrantLock] vs 1초[ReentrantReadWriteLock] 가 났다.
reader의 thread가 동시에 일어날 수 있기 때문에 시간이 더 빠르게 나왔다.

##### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/11200026#content
