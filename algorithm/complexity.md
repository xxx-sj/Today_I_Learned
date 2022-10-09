# complexity [복잡도]

- 시간 복잡도 : 실행에 필요한 시간을 평가한 것
- 공간 복잡도 : 기억 영역과 파일 공간이 얼마나 필요한가를 평가한 것


###### 선형 검색의 시간 복잡도

```java
static int seqSearch(int[] a, int n, int key) {

  int i = 0;
  whie(i < n) {
    if (a[i] == key) {
      return i; //성공
    }
    i++;
  }
  return -1; //실패
}
```

```
변수 i에 값을 할당하는 복잡도는 O(1)이라 할 수 있다.
여기서 a[1] == key 를 비교하는 평균 시간복잡도는 n / 2 이다. (1 ~ n-1)
n/2번 실행 했을 때 시간복잡도 표현은 O(n/2) 가 아닌 O(n)으로 표기한다.
이유는 n이 무한히 커진다고 한다면 값의 차이가 의미가 없기 때문이다.
마찬가지로 100번 실행 하는경우도 O(100)이 아닌 O(1)로 표기한다. 차이를 못 느낄 정도이기 때문이다.
O(n)은 n에 비례하여 점점 길어지지만 O(1)은 변하지 않는다.
```

###### O(f(n)과 O(g9n))의 복잡도 계산
```
O(f(n)) + O(g(n)) = O(max(f(n) + g(n)))
```


##### 이진 검색의 시간 복잡도

```java
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
```
###### 출처: 자료구조와 함께 배우는 
