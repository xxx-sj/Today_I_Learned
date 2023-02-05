# resource sharing and introduction to critical sections


### 리소스란 무엇인가
- 변수 (integers, Strings ..)
- Data structure - collection, maps, set ...
- file or connection handles [netwrok]
- message or work queues
- any objects..


database microservice 에서 모든 request들은 서로 다른 스레드에서 처리되지만 결국 모든 요청은 database를 읽거나 쓰게 된다.


### what is the problem of sharing resourcers
- 테스트로 하나의 inventory class 있다고 가정하자. inventory는 counter를 field로 갖고 있으며, counter를 증가하는 method오 감소시키는 메소드 2개가    
  있다. 
- 각각의 thread를 extend 하여 class를 2개 만드는데, 하나는 run에서 counter를 증가, 다른 하나는 감소 시키는 class를 만든다.
- 이제 main에서 두 개의 thread를 실행하게 되는데, 증가 시키는 class를 start 한 후 join을 call하여, run이 종료되면 그 다음 class인 감소 class를 start       
  시켜서 counter를 감소 시킨다. 모두 끝난 후 값을 print 해보면 0 이 나오는 것을 확인할 수 있다. 이와 반대로 2개의 thread를 동시에 실행 시키고 결과를 확인해보면
  0이 아닌 다른 값이 나오는 것을 확인할 수 있다.
  
```java
main {
  Inventory inven = new Inventory();
  IncreaseClass c1 = new IncreaseClass(inven);
  DecreaseClass c2 = new DecreaseClass(inven);
  
  c1.start();
  c2.start();
  
  c1.join();
  c2.join();
  
  println(inven.getCounter())


}
```

이러한 문제는 inven의 counter가 두 스레드에 공유된 개체이기 때문이다. 또한 counter를 증가 시키고 [ counter++ ] counter 를 감소시키는 [counter--]
행동이 동시에 일어나게 되며, single operation | atomic operations이 아니다.

### atomic operation 이란
- 연산 또는 연산의 집합은 atomic으로 고려되는데, 마치 한번에 일어난 것처럼 시스템의 나머지 부분에 나타난다.
- single step - all or nothing
- no intermediate states

실제로 counter++ 는 세 가지 작업이다.
1. 메모리에 저장된 counter의 value의 현재 값을 얻는다.
2. 현재 값을 1씩 증가 시킨다.
3. 증가시킨 결과값을 counter에 저장한다.

만약 지금 counter가 0 이라면
- counter를 메모리에서 가져온다 [0]
- counter를 1 증가 시킨다. [1]
- 증가시킨 counter를 저장한다. [1]
```
증가시키는 thread가 먼저 os에 의해 예약이 되어 첫 번째와 두 번째 작업을 하고 있다가, os에 의해 감소시키는 thread가 예약이 되어 
작업인, 값을 찾고, 감소 시키고, result를 저장하는 task를 끝낸 후 다시 스케쥴을 얻어 증가시키는 thread로 돌아와 증가시킨 값 1을 counter에 저장하게 되면
result값을 감소시킨 thread의 task는 overrload되어 버린다. 결국 counter에는 1이 저장되게 된다. 마치 감소 thread는 동작하지 않은 것처럼 일이 끝나버린다.
```


##### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/11199990#content
