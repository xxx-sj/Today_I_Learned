# OS fundamentals


### context switch    

프로세스는 각각 독립적으로 실행되며 프로세스는 하나 이상의 스레드를 가질 수 있다. 이러한 스레드는 CPU에서 실행된다.    
even if 코어가 여러개인 경우에도 여전히 코어보다 스레드가 많다.    
OS 하나의 스레드를 실행시킨 후 멈추고, 다른 스레드를 실행시키고 멈춘다.    
이러한 행동    
stop thread 1    
schedule thread 1 out    
schedule thread 2 in    
start thread 2    
을 context switch 라고 한다.    

context switch cost는 많은 스레드를 deal with 할때 not cheap 하고, is the price we need to pay for concurrency 도 not cheap 하다.    

* * *

만약 4개의 스레드 존재한다고 가정한다. (뮤직 플레이어의 UI 스레드와, 파일에서 로드하여 음악을 play하는 스레드),    
(텍스트 파일에서 UI담당과 2초마다 저장하는 스레드)     
각각의 스레드에 arrival order 순서대로 thread를 실행하게 된다.    
도착한 순서대로 1순위가 length가 가장 길다면 다음 스레드 실행까지의 시간이 오래걸려 사용자에게 불편함을 줄 수 있다.    
반대로, 짧은 length를 가진 thread 부터 실행을 하게 된다면, 시스템에는 항상 사용자 관련 이벤트가 발생하기 때문에, length 가 긴 thread는    
실행되지 않을 수 있다.

일반적으로 운영체제는 시간을 epochs라는 적당한 사이즈의 크기로 나눈다. epochs에는 운영체제가 서로 다른 스레드를 넣는다.    
각 스레드에 대한 시간 할당 방법에 대한 결정은 동적순위[ dynamic priority  = static priority + bonus ]     
에 따라 결정된다. static priority 는 개발자가 설정한 값이다.    
Bonus는 OS에 의해 모든 epoch의 thread는 조정된 값이다.









##### 참고: https://docs.oracle.com/javase/8/docs/api/java/lang/Thread.State.html
            https://developer.ibm.com/tutorials/l-completely-fair-scheduler/
            
##### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/11210906#content
