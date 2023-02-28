# 원자적_정수와_lock-free_전자_상거래

## AtomicInteger
```java
int initialValue = 0;
AtomicInteger atomicInteger = new AtomicInteger(initialValue);

//원자적으로 정수를 1 증가 시킨다.
//atomicInteger.incrementAndGet(); //return the new value;
atomicInteger.getAndIncrement(); // return the previous value'

//원자적으로 1을 감소시킴
atomicInteger.decrementAndGet(); 
atomicInteger.getAndDecrement();
```


```java
int initialValue = 0;
AtomicInteger atomicInteger = new AtomicInteger(initialValue);

int delta = 5;
//원자적으로 integer를 추가한다.
atomicInteger.addAndGet(delta); // return the new value
atomicInteger.getAndAdd(delta); // return the previous value
```

이러한 것에 장점은 락이나 동기화가 전혀 필요하지 않아 경쟁 상태나 데이터 경쟁을 걱정하지 않아도 된다.     
단점은 atomic 메서드 자체가 원자적이란 점인데 atomic 메서드를 다른 연산과 함꼐 원자적으로 실행할 수 없다.


ex)
```java
int initialValue = 0;
AtomicInteger atomicInteger = new AtomicInteger(initialValue);

atomicInteger.incrementAndGet();
atomicInteger.addAndGet(-5);       <---------- race condition [경쟁 상태]
```
각 연산이 원자적이지만 서로 분리되었기 때문이다.


##### 출처: https://www.udemy.com/course/java-multi-threading/learn/lecture/30800376#content
