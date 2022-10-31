# queue [큐]

- 일시적으로 쌓아 두기 위한 자료구조
- 선입선출
- 은행창구와 같이 대기열
- 데이터를 넣는 작업을 enqueue 꺼내는 작업을 dequeue
- 꺼내는 쪽을 front 데이터를 넣는 쪽을 rear
- 배열로 구성 시 dequeue하는 작업에서 shift를 통한 복잡도가 O(n)이 되어 버린다.
  - 이를 해결하기 위해 링 버퍼로 큐를 만들어본다.


```
링버퍼로 구현하기 위해 개념적으로 
변수 front와 rear를 만든다
front는 맨 처음 요소의 인덱스를 나타내고
rear는 맨 마지막 요소의 다음 인덱스를 나타낸다.(인큐랑 인덱스 미리지정)
처리복잡도는 O(1)이다.
```

```java
class Queue {
    public static void main(String[] args) {

    }
}


class IntQueue {
    int max; //큐의 용량
    int front; // 첫 번째 요소 커서
    int rear; // 마지막 요소 커서
    int num; // 현재 데이터 수: front와 rear의 값이 같을 때 비었는지 다 찼는지 확인하기 위해
    int[] que; //큐

    //실행 시 예외: 큐가 비어있음
    public class EmptyQueueException extends RuntimeException {
        public EmptyQueueException(){}
    }

    //실행 시 큐가 가득참
    public class OverflowQueueException extends  RuntimeException {
        public OverflowQueueException() {}
    }

    //생성자
    public IntQueue(int capacity) {
        num = front = rear = 0;
        max = capacity;
        try {
            que = new int[max];
        }catch (OutOfMemoryError e) {
            max = 0;
        }
    }

    public int enque(int x) throws OverflowQueueException {
        if(num >= max)
            throw new OverflowQueueException();
        //리어에 매번 넣는다.
        que[rear++] = x;
        //요솟값 증가
        num++;
        //리얼값을 증가시켰는데 해당 값이 max와 같다면 0으로 돌린다
        //max는 요소의 갯수 [max - 1 == maxIndex]
        if(rear == max) rear = 0;
        return x;
    }

    public int deque() throws EmptyQueueException {
        if (num <= 0)
            throw new EmptyQueueException();
        //꺼낼때는 front에서 꺼낸다.
        int x = que[front++];
        //들어가있는 요소갯수 감소
        num--;
        //만약에 front의 값이 max와 같다면 0으로 초기화
        if (front == max)
            front = 0;

        return x;
    }

    //큐에서 데이터를 피크 front값 확인하기
    public int peek() throws EmptyQueueException {
        if (num <= 0)
            throw new EmptyQueueException();
        return que[front];
    }

    //큐에서 x값을 검색 인덱스값 반환
    public int indexOf(int x) {
        //현재 들어있는 값 만큼 for문
        for (int i = 0; i < num; i++) {
            /*
            i 부터 선형검색하지 않는 이유는 해당 값이
            index = 0 부터 시작이 아니라 front부터 시작이기 때문에
            i가 아닌 front부터 index가 시작된다 따라서
            i는 front 부터 증가를 해야하므로 front + i 가 된다.
            max로 나머지를 구하는 이유는 max에 도달했을경우 max로 나누지 않는다면
            인덱스에 벗어나는 값을 찾으려고 하기때문이다.
            ex) max = 10; // 총 요소의 갯수
            i + front = 10; //index의 값은 9이므로 오류를 발생시킨다.
            나머지로 계산하므로서 (i+front) 가 10일 경우 max 로 나눈결과 0 이 된다.
             */
            int idx = (i + front) % max;
            if(que[idx] == x)
                return idx;
        }

        return -1;
    }

    //큐를 비움
    public void clear() {
        num = front = rear = 0;
    }

    //큐의 용량 반환
    public int capacity() {
        return max;
    }

    //큐에 데이터 갯수 반환
    public int size() {
        return num;
    }

    //큐가 비어있는지
    public boolean isEmpty() {
        return num <= 0;
    }

    //큐가 가득 찼는지
    public boolean isFull() {
        return num >= max;
    }

    //큐 안의 모든 데이터를 front -> rear 순으로 출력
    public void dump() {
        if (num <= 0)
            System.out.println("큐가 비어 있습니다.");
        else {
            for(int i = 0; i < num; i++) {
                System.out.println(que[(i + front) % max] + " ");
            }
            System.out.println();
        }
    }

}
```


##### 출처: 자료구조와 함께 배우는 알고리즘 입문
