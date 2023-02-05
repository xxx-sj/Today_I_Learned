# stack & heap memory regions

### stack
 각 프로세스는 적어도 하나의 스레드 [ 메인 스레드 ]와 시작된다.
 
 ```java
 
 void main(String[] args) {
   int x = 1;
   int y = 2;
   int result = sum(x, y);
 }
 
 int sum(int a, int b) {
  int s = a + b;
  return s;
 }

 ```
 [ main method ]
 스레드가 메서드로 이동하는 즉시 stack에 일부 공간을 할당합니다. 해당 공간을 stack frame이라고 부른다. 해당 메소드에 대한 인수는 
 메소드 내부의 스택에 푸시된다. 그리고, 모든 local variable은 스택에 할당되고, 순서대로 push 된다. [ args -> x=1 -> y=2 ]
 
 그 다음 int result 에 해당하는 line에서 다른 method를 호출하고 있는데, 여기서 main method stack 위에 sum method stack이 쌓이게 된다.    
 또한 해당 메서드로 전달된 arg 도 stack frame에 push된다. [a = 1, b=2]     
 각각의 메소드는 자신의 프레임에 있는 variables만 접근 가능하다.  sum method는 main에 할당되어있는 x와 y에 대해서는 알 수 없다.
 sum method의 마지막으로 s가 쌓이고 return s를 하게 되면, sum method에 대한 frame은 종료되고 result는 cpu의 특별한 register에 저장된다.   
 다음으로 명령 포인터가 caller 메소드로 돌아오고 sum method stack은 is invalideated 된다. sum에 return 값은 result에 저장되고 result 또한 main stack에     
 push 된다. 이 후 main method가 끝나면, stack frame 또한 invalidated 된다. 이것이 stack이라고 불리는 이유이다.   
 
 모든 메소드 프레임은 할당되고, invalidated 되고 last in, first out 순서로
 
 
 ### heap
- 프로세스에 속하는 공유 메모리 영역이다. thread는 이 heap에 저장된 데이터를 공유하여 사용할 수 있다.
- 모든 object는 heap에 저장된다. 기본적으로 new 연산자를 사용하여 할당할 수 있는 모든 것이다.
  - String
  - object
  - Collection
  - ...

- members of classes 도 모두 heap에 저장된다. primitive member든지, object 그 자체든 모두 heap에 저장된다.
- 또한 static variables 또한 힙에 위치한다.
  정적 변수들은 실제로 해당 클래스와 연결된 클래스 객체의 member이기 때문이다.

- heap은 Governed 그리고, 가비지 컬렉터에 의해 관리된다.
- member 들도 object 와 같은 life-cycle을 가지며, static 변수는 app이 실행되는 동안 heap에 영원히 머물게 된다.


### Objects vs References
```java
Object var1 = new Object();
Object ver2 = var1;
```
object와 reference는 다르다. 위에서 처럼 var1을 선언하고 var2에 var1을 초기화 하게되면, var1과, var2는 object의 주소를 갖고있는 변수가 되며.
메서드 내에서 references를 선언하게 되면. 선언된 값은 stack에 할당되고 만약, 클래스의 멤버라면, heap에도 할당된다.
반대로 obejcts는 항상 heap에 할당된다.

##### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/11199598#content
