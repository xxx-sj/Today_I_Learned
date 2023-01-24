# 스레드 상속

스레드 생성 시 Runnable 인터페이스를 익명 클래스로 정의하는 것 이외에도 Thread class를 생성하여 new로 만들 수 있다
Thread class 생성 시 에는 Thread를 extends 받아서 run method를 구현한다.     
Thread 내부를 보면, Runnable을 구현한 것을 확인할 수 있다.    

```java
package example2;

public class Main {
    public static void main(String[] args) {
        Thread thread = new NewThread();
        thread.start();
    }

    private static class NewThread extends Thread{
        @Override
        public void run() {
            System.out.println("Hello from" + Thread.currentThread().getName());
        }
    }
}
```

또한 Thread를 extends 하여 구현하게 되면 현재 스레드에 대한 정보를 많이 얻을 수 있다. method data etc...
run에서 println으로 출력하던 Thread.currentThread() method를 this로 변경할 수 있다.    
```java
private static class NewThread extends Thread{
    @Override
    public void run() {
        System.out.println("Hello from" + this.getName());
    }
}
```

```java
public class Main {
    public static final int MAX_PASSWORD = 9999;

    public static void main(String[] args) {
        Random random = new Random();

        Vault vault = new Vault(random.nextInt(MAX_PASSWORD));

        List<Thread> threads = new ArrayList<>();

        threads.add(new AscendingHackerThread(vault));
        threads.add(new DescendingHackerThread(vault));
        threads.add(new PoliceThread());

        for (Thread thread : threads) {
            thread.start();
        }
    }

    /**
     *
     * 금고 비밀번호가 기본값이며 비밀번호확인 method를 call 하면 0.05초
     * sleep 한다.
     */
    private static class Vault {
        private int password;

        public Vault(int password) {
            this.password = password;
        }

        public boolean isCorrectPassword(int guess) {
            try {
                Thread.sleep(5);
            } catch (InterruptedException e) {
            }
            return this.password == guess;
        }
    }

    /**
     * HackerThread 금고를 생성자를 통해 받으며, 이름과 우선순위를 설정한다.
     */
    private static abstract class HackerThread extends Thread {
        protected Vault vault;

        public HackerThread(Vault vault) {
            this.vault = vault;
            this.setName(this.getClass().getSimpleName());
            this.setPriority(Thread.MAX_PRIORITY);
        }

        @Override
        public void start() {
            System.out.println("Starting thread " + this.getName());
            super.start();
        }
    }

    /**
     * HackerThread를 extends 받아서 start() method call 이후
     * run에서 해당 thread가 무엇을 처리해야하는지 정의한다.
     * Max_PAssword만큼 loop를 돌면서 금고의 비밀번호를 check한다.
     * 맞으면 system을 종료시킨다.
     */
    private static class AscendingHackerThread extends HackerThread {

        public AscendingHackerThread(Vault vault) {
            super(vault);
        }

        @Override
        public void run() {
            for (int guess = 0; guess < MAX_PASSWORD; guess++) {
                if (vault.isCorrectPassword(guess)) {
                    System.out.println(this.getName() + " guessed the password " + guess);
                    System.exit(0);
                }
            }
        }
    }

    /**
     * HackerThread를 extends 받아서 start() method call 이후
     * run에서 해당 thread가 무엇을 처리해야하는지 정의한다.
     * Max_PAssword만큼 loop를 돌면서 금고의 비밀번호를 check한다.
     * 맞으면 system을 종료시킨다.
     */
    private static class DescendingHackerThread extends HackerThread {

        public DescendingHackerThread(Vault vault) {
            super(vault);
        }

        @Override
        public void run() {
            for (int guess = MAX_PASSWORD; guess >= 0; guess--) {
                if (vault.isCorrectPassword(guess)) {
                    System.out.println(this.getName() + " guessed the password " + guess);
                    System.exit(0);
                }
            }
        }
    }

    /**
     * policeThread 10초동안 count 하며 10초가 지나면 police가 이기게 된다.
     * 10초 이 후에는 thread를 종료시킨다.
     *
     */
    private static class PoliceThread extends Thread {
        @Override
        public void run() {
            for (int i = 10; i > 0; i--) {
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                }
                System.out.println(i);
            }

            System.out.println("Game over for you hackers");
            System.exit(0);
        }
    }
}


```

##### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/11198656#content
