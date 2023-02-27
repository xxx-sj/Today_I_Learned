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
  lock은 조건 확인과 조건에 들어있는 공유 변수의 수정이 원자적으로 실행되었는지 확인하는데 사용된다.

조건 변수는 스레드 간 통신의 제네릭 방법이다.    
스레드 A가 특정 조건을 확인할 수 있다. 조건이 충족되지 않으면 스레드A를 정지시킬 수 있고, 나중에 스레드 B가 상태를 변경해    
스레드 A를 깨우기 위해 신호를 보낼 때까지 기다려야 한다. 이 후 thread A는 조건을 다시 체크해 만족한다면 다음으로, 아니라면    
다시 기다리게 된다.

```java
Lock lock = new ReentrantLock();
Condition condition = lock.newCondition(); // 조건 객체 생성
String username = null, password = null; //share variables 공유변수

//인증 스레드
lock.lock();
//try 블록에 관심있는 조건이 충족됐는지 확인한다.
try {
//username과 password가 채워졌는지 확인
  while(username == null || password == null) {
    condition.await(); //조건이 충족되지 않으면 while 로 들어와  await method를 호출한다. // unlocks lock, and puts thread to sleep
    //await 메서드는 다른 스레드가 조건 신호를 보낼때까지 스레드를 슬립 시키고 스레드가 슬립하기 전에 await 메서드는 lock을 원자적으로 unlock한다.
  }
}
finally {
  lock.unlcok();
}
doStuff();


//await method가 원자적으로 unlock하는게 중요한데, UI스레드가 lock을 얻을 수 있기 때문이다.
//UI thread
lock.lock()
try {
  username = userTextBox.getText(); //입력한 값 username에 대입 없으면 null
  password = passwordTextBox.getText();//입력한 값 password에 대입 없으면 null
  
  condition.signal(); // 인증스레드를 깨우기 위해 signal method 호출
}
fanllay [
  lock.unlcok();
}

signal로 인증 스레드가 깨어나도, lock을 얻을 때 까지 계속 깨어 있지 않을 것이다. 따라서, UI thread가 우선 lock을
unlock 해야 인증 스레드가 지속된다. 다시 인증 스레드가 lock을 얻게 되면 while 루프의 다음 명령어로 가는데 이 경우에는 
조건을 다시 화깅ㄴ하라는 내용이다. 조건이 충족되면 인증 스레드는 우선 lock을 unlock하고 do Stuff() 메서드로 넘어가 
데이터 베이스로 가서 사용자가 존재하는지, 암호가 맞는지 확인한다. 만약 공유변수가 null인 경우 인증스레드는 다시 calling the await 메소드로 가서
lock을 다시 unlock하고 다음 신호를 계속 기다릴 것이다.
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
