# algorithm_analysis [알고리즘 분석]

```java
    static void recur(int n) {
        if (n > 0) {
            recur(n - 1);
            System.out.println(n);
            recur(n -2);
        }
    }
```

##### 하향식 분석
/ n에 4가 주어졌을 때
1. recur(3)을 실행한다.
2. 4를 출력한다.
3. recur(2)를 실행한다.

물론 2번 처럼 바로 4를 출력하지 않고, 1에서 실행된 재귀호출이 끝나고 나서야 출력된다.

##### 가장 위쪽에 위치한 상자의 메소드 호출부터 시작해 계단식으로 자세히 조사하는 분석 기법을 하향식 분석이라고 한다.


##### 상향식 분석
##### 위쪽부터 분석하는 하향식 분석과는 대조적으로 아래쪽부터 쌓아 올리며 분석하는 방법

```
recur 메서도는 n이 양수일 때만 실행하므로 (n > 0) 먼저 recur(1)을 생각해보자.
1. recur(0)을 실행한다.
2. 1을 출력한다
3. recur(-1)을 실행한다.

다음으로는 recur(2)를 생각해보자
1. recur(1)을 실행한다.
2 2를 출력한다.
3. recur(0)을 실행한다.
```


##### 재귀 알고리즘의 비재귀적 표현
```
recur 메소드를 재귀호출을 사용하지 않고 구현하기
recur 메소드의 끝부분 recur(n - 2) 부분은 '인자로 n -2를 전달하여 recur 메소드를 호출한다'라는 의미이다. 
따라서 n의 값을 n - 2로 업데이트하고 메서드의 시작 지점으로 돌아간다 라고 변경할 수 있다.
```

```java
static void recur2(int n) {
        while(n > 0) {
            recur2(n -1);
            System.out.println(n);
            n = n - 2;
        }
    }
```

##### 재귀의 제거
```
앞서 변경했던 꼬리재귀는 처리할 수 있지만. recur(n -1) 메소드의 재귀는 어렵다.    
왜냐하면 변수n의 값을 출력하기 전에 recur(n - 1)을 먼저 수행해야한다. 
n의 값이 4일경우 재귀호출 recur(3)이 완료되지않으면 출력할 수 없으므로 n의 값인 4를 해당 호출부분에서 저장해야한다. 

1. n의 값을 n - 1로 업데이트하고 시작지점으로 돌아간다.
2. # 돌아가기전 n의 값을 임시로 저장한다.
3. 재귀호출이 끝나면 저장했던 값을 출력한ㄷ,.
```


```java
IntStack s = new IntStack(n);

static void recur3(int n) {
        while(true) {
        //recur(n - 1)
            if (n > 0) {
                s.push(n);
                n = n -1;
                //처음으로 돌아간다.
                continue;
            }

        //비어있지 않다면 
            if (s.isEmpty() != true) {
                n = s.pop();
                //출력
                System.out.println(n);
                //recur(n - 2)
                n = n - 2;
                continue;
            }

            break;
        }
    }

```


##### 출처: 자료구조와 함꼐 배우는 알고리즘 
