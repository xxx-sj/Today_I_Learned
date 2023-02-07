# race conditions and data races

### 경쟁 상태 [ race condition ]
- 여러 스레드가 공유 리소스에 액세스하는 경우의 조건
- 이러한 스레드 중 하나 이상이 공유 리소스를 수정하고 있을 때
- 타이밍이나, 스레드 스캔의 순서가 잘못된 결과를 발생할 수 있다.
- 문제의 핵심은 공유 리소스에 대한 비원자적 실행 [ non atomic operations ] 작업



### 경쟁 상태 해결법
- 경쟁 상태가 발생하는 임계구역의 식별
- synchronized block을 이용한 임계구역의 보호  

### 데이터 레이스 [ data race ]
ex) 
```java
한개의 메소드는 공유되는 자원인 x, y 를 증감 연산자로 증가시키고 있다.

void method() {
x++;
y++;
}

또 다른 메소드는 공유 자원인 x 와 y의 크기를 비교한다.

void method2() {

if (y > x) {}
}

여기서 두 개의 method 서로다른 thread가 호출을 하게 되면 data race가 발생하게 되는데,
x가 증가하고 y가 증가하기 전, method2가 호출되어 call되면 ( x > y )식이 성립되게 되며, 만약 반대라면
( y > x ) condition이 성립되어 if문 안에 logic이 실행되게 된다. 또 다른 예시로 y에 값이 대입되고 나서,
증가 시키는 method가 매번 점유하여 실행되었다면, 그 후 읽는 x의 값은 더 많이 증가되어 있을 것이다.
y <- 1이 되고, x <- 34가 될수도 있디.
위에서 볼 수 있듯이 x가 먼저 증가하고, y가 그 다음 증가하게 되며, method2에서는 y의 값을 먼저 보고, 이 후 x의 값을 보게된다.
이 예시에 맞게 비교연산자를 보면, [ x >= y] 가 되게 될 것이다.
```

하지만 이러한 가정이 [ x >= y] 막상 실행을 하고보면 y > x condition이 true인 경우가 있어 if문이 실행되는 경우가 있다.
이러한 경우를 data race라고 한다.
- 컴파일러와 CPU는 성능과 활용도를 최적화하기 위해 명령을 비순차적으로 실행할 수 있습니다.
- 그들은 코드의 논리적 정확성을 유지하면서 그렇게 할 것입니다.
- 컴파일러와 CPU에 의한 비순차 실행은 코드 속도를 높이는 중요한 기능입니다.
    비순차 실행이 안된다면 프로그램은 더 느리게 실행될 것이다.
- 컴파일러는 더 나은 명령을 위해 명령을 재정렬합니다.
    - 분기 예측(최적화된 루프, "if" 문 등)
    - 벡터화 - 병렬 명령 실행(SIMD)
    - 캐시 명령 미리 가져오기 - 캐시 성능 향상
- CPU는 더 나은 하드웨어 장치 활용을 위해 명령을 재정렬합니다.


```java
void method () {
x = 1;
y = x + 2;
z = y + 10;
}
```
위의 예제는 이 전 결과값에 의존하기 때문에 data race가 발생하지 않는다.    
컴파일러나 cpu가 순서를 변경하려하면, 논리적으로 맞지 않기 때문이다.

```java
void method() {
x++;
y++;
}

void method2() {
y++;
x++;
}
```
cpu나 compiler 입장에서는 두 개의 메소드는 논리적으로 동일하며, 싱글 스레드에서는 문제가 발생하지 않는다.     
문제는 컴파일러와 cpu가 다른 코어에서 실행되고 있는 thread에 대해 인식하지 못하는 것, 동일한 변수를 읽고 특정 실행 순서에 따라 실현

### 결과 [ consequences ]
- 예상치 못한 역설적이며 잘못된 결과를 초래할 수 있습니다.


### data race를 피하는 방법 - solution
- 공유변수를 수정하는 메소드에 synchronization 을 사용하는 것 
    reordered 되더라도 하나의 스레드만 공유 변수에 접근할 수 있기 때문에  data race가 일어나지 않는다.
- volatile 키워드로 공유 변수 선언
    오버헤드가 줄고, order가 보장된다.
    
    
    
    
### Volatile
주의해서 사용해야 한다.   
long 및 double에 대한 read/write, form/to  race condition 을 해결합니다    
data race의 경우 volatile keyword는 order를 보장한다.   
solves all data races by guaranteeing order

##### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/11200014#content
