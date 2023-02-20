# condition variables - all purpose, inter-thread communication

## review of inter-thread communication
- 스레드A에서 interrupt() method를 사용하면 다른 스레드에 신호를 보냈었다.
- 이 신호는 스레드 B에게 알리기 위해 하나의 컨텍스트에서만 사용되었다.

Thread.join();
ThreadB.join()을 하면, threadA.join()을 호출하면 threadB는 a가 끝나기 까지 기다리게 됨.
Semaphore threadA가 acquire()을 하면, threadB가 release할 때까지 block 됨.

## Semaphore as Condition var
- calling the acquire() on a semaphore is equivalent to checkingthe condition "is number of permits > 0 ? " 
  [ 세마포어에 대해 acquire()를 호출하는 것은 "permite 수가 0보다 큰가? "라는 조건을 확인하는 것과 같다. ]
  이것은 스레드A가 다음 명령으로 이동하기 위해 요구하는 조건이다.
- if the condition is not met - thread a goes to sleep until another thread changes the semaphore's state
  [ 조건이 충족되지 않으면 (permit > 0) 다른 스레드가 세마포어의 상태를 변경할 때까지 스레드 a가 휴면 상태가 됩니다. ]
- thread b가 release() method를 호출하게 되면 threaA가 깨어나서
- threadA가 조건을 체크한다. permit의 숫자가 0 보다 큰가?
  - 만약 그렇다면 threadA는 이어서 다음  명령을 실행합니다.


## condition variable
- condition variable는 항상 lock과 연결됩니다
- lock은 조건과 관련된 공유 변수의 원자 검사 [atomic check] 및 수정[modification]을 보장합니다.

```java
Lock lock = new ReentrantLock();
Condition condition = lock.newCondition();
String username = null, password = null; //share variables

lock.lock();
try {
  while(username == null || password == null) {
    condition.await();  //조건이 충족되지않으면 임의의 값으로 채워진 다음 // unlocks lock, and puts thread to sleep
    //await method는 thread를 sleep로 전환하며,
  }
}
finally {
  lock.unlcok();
}
doStuff();


//UI thread
lock.lock()
try {
  username = userTextBox.getText();
  password = passwordTextBox.getText();
  condition.signal();
}
fanllay [
  lock.unlcok();
}

ui thread로 signal을 보내 왼쪽 thread를 깨워도 바로 사용하지 못한다. lock을 ui thread에서 unlock 한 후 사용 가능해짐
while loop condition을 다시 check한다. 조건이 충족되면 unlcok하고 database로 이동하는 do stuff method를 실행한다.
만약 name and password가 null이라면 await method를 call하고, unlcok the lock again 하고 다음 signal을 기다린다.
```

## Condition.await()
- void await() - > unlcok lock, wait until signalled // puts thread to sleep
  await method는 puts the thread to sleep completely giving up its CPU until another thread signals it to wake up
  다른 스레드가 깨우라는 신호를 보낼 때까지 CPU를 완전히 포기하고 스레드를 잠자기 상태로 만듭니다.
  and before doing so it unlocks the lock associated with the condition variable
  그렇게 하기 전에 조건 변수와 관련된 lock을 해제합니다.
- long awaitNanos(long nanosTimeout) -> wait no longer than nanosTimeout
- boolean await(long time, TimeUnit unit) -> wait no longer than time, in given time units
- boolean awaitUntil(Date deadline) -> wake up before the deadline date // until

## Condition.signal()
- void signal() - wakes up a single thread, waiting on the condition variable
                현재 조건 변수를 기다리고 있는 스레드를 깨운다. / 조건변수를 기다리는 스레드가 둘 이상인 경우,    
                해당 스레드 중 하나만 실행된다. 나머지 스레드는 sleep 상태를 유지된다.
- a thread that wakes up has to reacquire the lock associated with the condition variable
  [ 깨우는 스레드는 조건 변수와 관련된 잠금을 다시 획득해야 합니다.  await한 thread ]
  
- if currently no thread is waiting on the condition variable, the signal method doesn't do anything
  [ 현재 조건 변수에서 대기 중인 스레드가 없으면 신호 메서드는 아무 작업도 수행하지 않습니다. ]
  조건 변수가 시그널링되었다는 사실은 어디에도 저장되지 않고, await를 호출할 앞으로의 스레드는 영향을 받지 않는다. 
  
## Condition.signalAll()
- void signalAll() - broadcast a signal toall threads currently waiting on the condition variable 
    [ 현재 조건 변수를 기다리고 있는 스레드에 신호를 브로드캐스트합니다. ] 이 케이스경우, 모든 스레드가 깨어난다.
- 얼마나 많은 스레드가 조건 변수를 기다리고 있는지 알 필요가 없습니다.

이것이 세마포어와 다른 점이다, 세마포어의 경우 현재 대기중인 스레드 수 만큼 release를 해주어야 한다.


##### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/11200072#content
