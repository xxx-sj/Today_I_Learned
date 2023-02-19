# user interface application 예제

대시보드 작성
2개의 스레드
UI스레드 - 애니메이션 및 화면 이벤트에 응답하고 해당 자산의 현재 가격도 표시
백드라운드 스레드 - ㅂ네트워크 호출, 최신 가격을 주기적으로 업데이트
2개의 스레드 간에 공유 리소스가 필요하다. 
worker thread는 모든 가격을 업데이트 할 수있고 UI스레드는 새 가격을 가져와서 보여준다.

worker thread의 시간을 5초로 늘리면 가격 update가 덜 자주 업데이트 되는 것을 볼 수 있다. 그러나 응용 프로그램은 빠르고 반응이 좋다.     
UI 스레드에서 tryLock을 사용하지 않고 일반적인 lock 방식을 사용한다면 응용 프로그램이 얼마나 응답하지 않는지 확인할 수 있다.
배경화면 변화도 느리고 전반적인 ux는 매우 떨어진다.

#### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/11200024#overview
