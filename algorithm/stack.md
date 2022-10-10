# stack [스택]

- 일시적으로 저장하기 위해 사용하는 자료구조
- 후입선출
- 데이터를 넣는 것을 push 꺼내는 것을 pop


```java
class Stack {
    public static void main(String[] args) {
        
    }
}

class IntStack {
    int max;
    int pointer;
    int[] stack; //스택 레퍼런스 변수

    public class EmptyIntStackException extends RuntimeException {
        public EmptyIntStackException() {}
    }

    public class OverflowIntStackExcpetion extends RuntimeException {
        public OverflowIntStackExcpetion() {}
    }

    public IntStack(int capacity) {
        pointer = 0;
        max = capacity;
        try {
            stack = new int[max];
        } catch (OutOfMemoryError e) {
            max = 0;
        }
    }

    public int push(int x) throws OverflowIntStackExcpetion {
        if (pointer >= max) {
            throw new OverflowIntStackExcpetion();
        }
        return stack[pointer++] = x; //후위연산으로 넣은 후 증가
    }
    
    public int pop(int x) throws EmptyIntStackException {
        if (pointer <= 0) {
            throw new EmptyIntStackException();
        }
        return stack[--pointer]; //포인터 먼저 감소 후 값 꺼내기
    }
    //스택에서 상단의 있는 데이터 확인하기 (이전 데이터)
    public int peek() throws EmptyIntStackException {
        if (pointer <= 0)
            throw new EmptyIntStackException();
        return stack[pointer - 1];
    }

    //스택에서 x 값 찾기
    //해당 메소드는 선형검색을 통해 꼭대기부터 바닥으로 검색을 한다.
    public int indexOf(int x) {
        /*
        * i의 값을 point - 1 로 잡는 이유
        * pointer 증가 시작은 1 인덱스의 시작은 0
        *
        * */
        for (int i = pointer - 1; i >= 0; i--) {
            if (stack[i] == x) {
                return i;
            }
        }

        return -1;
    }

    /*
    * 데이터를 초기화하는 것이 아닌 포인터의 값을 0으로 돌린다.
    * */
    public void clear() {
        pointer = 0;
    }

    //스택의 용량을 반환
    public int capacity() {
        return max;
    }

    //스택에 쌓여 있는 데이터 [수] 반환
    public int size() {
        return pointer;
    }

    //스택이 비어있는가?
    public boolean isEmpty() {
        return pointer <= 0;
    }

    //스택이 가득 찼는가?
    public boolean isFull() {
        return pointer >= max;
    }

    //스택 안의 모든 데이터를 바닥에서 꼭대기순서로 출력
    public void dump() {
        if(pointer <= 0) {
            System.out.println("스택이 비어 있습니다.");
        } else {
            for(int i = 0; i < pointer; i++) {
                System.out.println(stack[i] + "");
            }
            System.out.println();
        }
    }
}
```


```java
import java.util.Objects;

class Stack {
    public static void main(String[] args) {
        
    }
}

class IntStack<T> {
    int max;
    int pointer;
    T [] stack; //스택 레퍼런스 변수

    public static class EmptyException extends RuntimeException {
        public EmptyException() {}
    }

    public static class OverflowStackException  extends RuntimeException {
        public OverflowStackException () {}
    }

    public IntStack(int capacity) {
        pointer = 0;
        max = capacity;
        try {
            stack = (T[]) new Object[max];
        } catch (OutOfMemoryError e) {
            max = 0;
        }
    }

    public T push(T x) throws OverflowStackException {
        if (pointer >= max) {
            throw new OverflowStackException();
        }
        return stack[pointer++] = x; //후위연산으로 넣은 후 증가
    }
    
    public T pop(T x) throws EmptyException {
        if (pointer <= 0) {
            throw new EmptyException();
        }
        return stack[--pointer]; //포인터 먼저 감소 후 값 꺼내기
    }
    //스택에서 상단의 있는 데이터 확인하기 (이전 데이터)
    public T peek() throws EmptyException {
        if (pointer <= 0)
            throw new EmptyException();
        return stack[pointer - 1];
    }

    //스택에서 x 값 찾기
    //해당 메소드는 선형검색을 통해 꼭대기부터 바닥으로 검색을 한다.
    public int indexOf(int x) {
        /*
        * i의 값을 point - 1 로 잡는 이유
        * pointer 증가 시작은 1 인덱스의 시작은 0
        *
        * */
        for (int i = pointer - 1; i >= 0; i--) {
            if (Objects.equals(stack[i], x)) {
                return i;
            }
        }

        return -1;
    }

    /*
    * 데이터를 초기화하는 것이 아닌 포인터의 값을 0으로 돌린다.
    * */
    public void clear() {
        pointer = 0;
    }

    //스택의 용량을 반환
    public int capacity() {
        return max;
    }

    //스택에 쌓여 있는 데이터 [수] 반환
    public int size() {
        return pointer;
    }

    //스택이 비어있는가?
    public boolean isEmpty() {
        return pointer <= 0;
    }

    //스택이 가득 찼는가?
    public boolean isFull() {
        return pointer >= max;
    }

    //스택 안의 모든 데이터를 바닥에서 꼭대기순서로 출력
    public void dump() {
        if(pointer <= 0) {
            System.out.println("스택이 비어 있습니다.");
        } else {
            for(int i = 0; i < pointer; i++) {
                System.out.println(stack[i] + "");
            }
            System.out.println();
        }
    }
}


```

###### 출처: 자료구조와 함께 배우는 알고리즘 입문
