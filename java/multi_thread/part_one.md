# motivation and os fundamentals 

처음 컴퓨터를 켜게되면 disk[보조 기억장치]에서 memory[주 기억장치]로 OS가 load된다.    
OS는 takes over 하고 provides an obstrction 한다. 그리고 interact with the HDD and cpu 하게 도와준다.    
프로그램을 실행하면 OS는 프로그램을 가져와 memory에 프로그램의 인스턴스르 만든다.    
프로그램 인스턴스는 프로세스라고 부르며 때로는 context of an application 이라고도 불린다.    
각각의 프로세스는 시스템에서 실행되는 다른 프로세스와 격리되어 있다.    

프로세스에는 몇 가지 사항이 포함된 메타데이터가 있는데, PID [process id ], 응용 프로그램이 읽고 쓰기 위해 여는 파일 및 CPU에서     
실행될 프로그램 코드들, Heap [data] (application 에 필요한 데이터들)  그리고 Main Thread가 포함되어있다.    
Thread에는 stack과 instruction pointer [명령어 포인터]가 존재한다.    

멀티 스레드에서는 각각의 thread에 stack과 pointer가 존재하게 된다. 그리고 프로세스의 나머지 구성요소는 스레드간 공유가 된다.    
스레드 내부의 stack은 지역 변수가 저장되고 함수가 실행되는 메모리 영역이다.      
그리고 instruction pointer는 스레드가 다음으로 실행할 포인터의 주소를 가리킨다.    
  
##### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/10187964?start=240#overview
