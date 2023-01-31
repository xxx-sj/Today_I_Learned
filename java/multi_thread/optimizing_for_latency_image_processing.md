# optimizing for latency - image processing


이미지를 처리할 때 병렬로 처리하는 것이다.
예시의 코드는 하나의 픽셀의 값을 가지고 새로운 이미지를 만드는 것인데, 왼쪽 0,0 부터 시작하여 모든 이미지를 하나의 스레드로 만드는 것은
오래 걸릴 수 있다. 이러한 문제를 병렬로 처리를 하여 렌더링을 향상 시킨다.    
스레드가 있는 만큼이미지를 여러 조각으로 나누기만 하면 된다.     
각 스레드는 해당 부분만 처리합니다. 그리고 모든 스레드가 완료되면 image buffer를 file에 저장한다.

##### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/11199862#content
