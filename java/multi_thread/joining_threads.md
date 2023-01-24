# joining threads

시나리오가 thread a 가 끝마친 결과를 가지고 thread b 가 사용해야한다 가정하자.    
이럴경우 간단한게 thread b에서 loop 를 계속 돌면서 a 가 끝났는지 check하게 코드를 짤 수 있을 것이다.     
하지만 이 경우 thread b가 check를 계속하게 되면 cpu 는 burn 하게 될 것이다.      
또한 cpu에서 보면     
흐름대로    
thread b check -> thread a doing -> thread b check -> ...
이런식으로 되게 될 텐데  thread b check 하는 리소스는 낭비가 될 것이고 thread a working에 방해가 될 것이다.   
이러한 상황에서 사용하는 method가 Thread.join() 메소드 이다.   
thread b는 기다리는 상태에서 a가 끝나면 동작한다.

```java

public class Main {
    public static void main(String[] args) throws InterruptedException {
        List<Long> inputNumbers = Arrays.asList(100000000L, 3435L, 35435L, 2324L, 4656L, 23L, 5556L);

        List<FactorialThread> threads = new ArrayList<>();

        for (long inputNumber : inputNumbers) {
            threads.add(new FactorialThread(inputNumber));
        }

        for (Thread thread : threads) {
            thread.setDaemon(true);
            thread.start();
        }

        for (Thread thread : threads) {
            thread.join();
        }

        for (int i = 0; i < inputNumbers.size(); i++) {
            FactorialThread factorialThread = threads.get(i);
            if (factorialThread.isFinished()) {
                System.out.println("Factorial of " + inputNumbers.get(i) + " is " + factorialThread.getResult());
            } else {
                System.out.println("The calculation for " + inputNumbers.get(i) + " is still in progress");
            }
        }
    }

    public static class FactorialThread extends Thread {
        private long inputNumber;
        private BigInteger result = BigInteger.ZERO;
        private boolean isFinished = false;

        public FactorialThread(long inputNumber) {
            this.inputNumber = inputNumber;
        }

        @Override
        public void run() {
            this.result = factorial(inputNumber);
            this.isFinished = true;
        }

        public BigInteger factorial(long n) {
            BigInteger tempResult = BigInteger.ONE;

            for (long i = n; i > 0; i--) {
                tempResult = tempResult.multiply(new BigInteger((Long.toString(i))));
            }
            return tempResult;
        }

        public BigInteger getResult() {
            return result;
        }

        public boolean isFinished() {
            return isFinished;
        }
    }
}
```

해당 예제는 array로 주어진 값을 thread를 이용하여 factorial을 계산하는 logic이다.     
중간에 thread.join()을 이용하여 새로만든 thread들이 동작이 마무리 되기 전 까지 main thread를 대기 시킨다.      
그 이 후 아래에 for loop를 동작시켜 threads에서 하나 씩 꺼내어 값을 출력하게 된다.     

계산하는데 큰 숫자를 넘기게 된다면 main thread는 해당 thread가 동작이 마무리 되기 전 까지 대기상태로 있을 것이다.
이러한 경우 join method의 인자로 timeout(ms) 값을 넘겨 timeout 만큼 wait 시키고 timeout이 지나면 main thread를 다시 동작하게 만들 수 있다.      
하지만 thread가 멈춘것은 아니기 때문에 만약 thread를 멈추게 하고 싶다면 thread.setDaemon()을 통해 데몬 스레드로 생성한다.    
또는 interrupt을 통한 thread를 안전하게 종료시키는 방법도 있다.
```java
for (Thread thread : threads) {
    thread.setDaemon(true);
    thread.start();
}

for (Thread thread : threads) {
    thread.join(2000);
}
```

##### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/11198682#content
