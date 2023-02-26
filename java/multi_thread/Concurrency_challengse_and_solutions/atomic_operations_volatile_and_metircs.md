# atomic operations, volatile & metrics 

### atomic operations
- 모든 참조 할당은 atomic이다.
    Object a = new Object();
    Object b = new Object();
    a = b; // atomic
- we can get and set referencesto objects atomically.

즉, object의 reference를 single operation에서 안전하게 변경할 수 있다.
모든 getter 및 setter은 arrays, string, or any objects 들은 atomically 하게 수행된다. 또한 동기화할 필요가 없다.
- 모든 원시타입 할당 도 안전하다. [ long과 dobule만 제외하고, ]

long 과 double 과 같은 문제를 해결하기 위해 java에서 [Volatile] 키워드를 지원한다. volatile keyword를 사용하면 
thread에 safe하며, atomic 하다. 다른말로 single hardware operation을 보장 받는다.   
이러한 keyword 덕분에 synchronized 없이 사용 가능해진다.

```java
public static class Metrics {
    private long count = 0;
    private volatile double average = 0.0;

    //capture method
    public synchronized void addSample(long sample) {
        double currentSum = average * count;
        count++;
        average = (currentSum + sample) / count;
    }

    public double getAverage() {
        return average;
    }
}
```
에서 getAverage에 synchronized 쓰지 않아도 되는 이유는 원시타입은 atomic이기 때문에사용하지 않고,
double 형은 thread safe 하지 않기 때문에, synchronized 또는 valitile keyword를 사용해줘야 한다.    
valitile을 사용함으로써 addSample method의 average에 할당하는 부분과 getter가 atomic해 진다.

##### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/11200012#content
