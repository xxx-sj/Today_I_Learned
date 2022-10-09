# binary_search [이진 검색]

###### 전제조건 데이터가 키 값으로 이미 정렬이 되어 있어야 한다.
###### 이진검색은 선형 검색보다 좀 더 빠르다.


- 맨 앞 인덱스를 pl 맨 끝 인덱스를 pr은(n - 1) [인덱스는0부터 시작] 마지막으로 중앙 인덱스를 pc((n -1) / 2)로 하자. (찾고자 하는 값은 k)
- k의 값이 arr[pc]보다 작다면 (arr[pc] > k) 범위를 arr[pc] 보다 작은 pl ~ arr[pc - 1]로 좁힌다.
- k의 값이 arr[pc]보다 크다면 (arr[pc] < k) 범위를 arr[pc] 보다 큰 arr[pc + 1] ~ pr로 좁힌다.
- 알고리즘의 종료조건은 2가지이다.
  - a[pc] 와 k 가 일치하는 경우
  - 범위 내에 찾으려는 값이 없는 경우
- 검색을 반복할 때마다 검색 범위가 절반이 되므로 검색에 필요한 비교 횟수의 평균은 logn 이고 검색에 실패할 경우 log(n + 1)이며 성공 시 대략 log(n -1)이다.


```java
class BinSearch {
    public static void main(String[] args) {
        int num = 20;
        int[] x = new int[num];
        for (int i = 0; i < num; i++) {
            x[i] = i;
        }

        int key = 12;

        int idx = binSearch(x, num ,key);

        if (idx == -1) {
            System.out.println("해당 값이 없습니다.");
        } else {
            System.out.println("해당 값은 [" + idx + "]에 있습니다.");
        }

    }

    static int binSearch(int[] a, int n, int key) {
        int pl = 0;
        int pr = n - 1;

        do {
            int pc = (pl + pr) / 2;
            if (a[pc] == key)
                return pc;
            else if (a[pc] < key) {
                pl = pc + 1;
            } else {
              pr = pc - 1;
            }
        } while (pl <= pr);

        return -1;
    }
}
```

##### 이진 검색을 하는 메소드 표준 라이브러리로 제공한다.
##### Arrays.binarySearch

###### 출처: 자료구조와 함께 배우는 알고리즘 입문 
