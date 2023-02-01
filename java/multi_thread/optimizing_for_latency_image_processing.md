# optimizing for latency - image processing


이미지를 처리할 때 병렬로 처리하는 것이다.
예시의 코드는 하나의 픽셀의 값을 가지고 새로운 이미지를 만드는 것인데, 왼쪽 0,0 부터 시작하여 모든 이미지를 하나의 스레드로 만드는 것은
오래 걸릴 수 있다. 이러한 문제를 병렬로 처리를 하여 렌더링을 향상 시킨다.    
스레드가 있는 만큼이미지를 여러 조각으로 나누기만 하면 된다.     
각 스레드는 해당 부분만 처리합니다. 그리고 모든 스레드가 완료되면 image buffer를 file에 저장한다.      

물리적 코어 수에 맞춰 thread를 늘리면 latency가 줄어드는 것을 확인할 수 있다.
물리적 코어 수를 넘어 가상 코어의 수만큼 thread를 늘리게되면 어느순간 값이 latency의 감소세가 줄어들어 비생산적이게 되는 부분이 된다.    
이유는 일부 리소스를 서로 공유하기 때문인데, benchmark가 완전히 컴퓨터가 실행한게 아니기 때문이다. [모든자원을 다 쓴게 아니기 때문에]
os process나 큰 application 의해 cpu가 소비되고 있을 수 있다. 가상 코어를 넘어 thread를 생성하게 되면 이점이 없다는 것을 확인할 수 있다.       
이미지의 크기가 클 수록 [pixel 수가 많을 수록] thread 당 속도의 비율은 점점 차이가 나게된다. 하지만 픽셀이 적어지면 이점이 적어지다가 어느순간
multithread의 이점이 -가 되는 구간이 오게된다.





##### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/11199862#content
