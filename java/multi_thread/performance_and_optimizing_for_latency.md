# 퍼포먼스 소개와 지연시간을 위한 개선 (optimizing)

- Latency [ 지연 시간 ] : 일의 완료 시간. time units으로 측정된다.    
- Throughput [ 처리량 ]: 주어진 기간동안 완료된 작업의 양. tasks/time unit으로 측정된다. (시간 당 작업)    

* * *

다중 스레드 프로그래밍에서 대기 시간[latency]를 어떻게하면 줄일 수 있을까?    
이론적으로는,     
만약 단일 스레드에서 순차적으로 완료할 수 있는 단일 작업이 있는 경우 우리는 여러가지의 독립적인 작업으로 나눌 수 있다고 생각한다.    
그런 다음 해당 작업들을 서로 다른 스레드에서 병렬로 실행하도록 예약한다.[ scheduled ]     
우리가 달성하고자 하는 것은 T[time] 대기시간을 N(number of sub tasks)로 나눈 값이다.

- N = ?
  오리지널 task에서 얼마나 많이 subtasks/threads 로 break 해야 하는가
  

* * * 
N = ?
만약 우리가 tasks를 원하는 만큼 sub task로 나눌 수 있다면 what would be that number?
일반적인 컴퓨터에서 N의 값은 = 코어의 수 이다. 지연 시간에서 감소의 이유는 sub task가 완전히 병렬로 동작한 결과이기 때문이다.

task가 더 무겁고 시간이 길수록 단일로 처리하는 것 보다 병렬로 처리하는 비용이 더 저렴하다.


##### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/11199858#content 
