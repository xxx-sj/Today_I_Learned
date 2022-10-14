# shell_sort [셸 정렬]

##### 단순 삽입 정렬의 장점은 살리고 단점은 보완하여 좀 더 빠르게 정렬하는 알고리즘

```
정렬을 마쳤거나 정렬을 마친 상태에 가까우면 정렬 속도가 매우 빨라진다. [장점]
삽입할 위치가 멀리 떨어져 있으면 이동(대입)해야 하는 횟수가 많아진다. [단점]

정렬할 배열의 요소를 그룹으로 나눠 각 그룹별로 단순 삽입 정렬을 수행하고,
그 그룹을 합치면서 정렬을 반복하여 요소의 이동 횟수를 줄이는 방법이다.
h-정렬
```

```java
class ShellSort {
    public static void main(String[] args) {
        
    }

    static void shellSort(int[] a, int n) {
        /**
         * h의 값을 초기화한다.
         * h정렬에서 h의 값 [4...2...1 ...]
         * h의 값은 0 전까지 순회한다.
         * 
         */
        for (int h = n / 2; h > 0; h /= 2) {
            for (int i = h; i < n; i++) {
                int j;
                int tmp = a[i];
                for (j = i - h; j >= 0 && a[j] > tmp; j -= h) {
                    a[j + h] = a[j];
                    a[j + h] = tmp;
                }
            }
        }
    }
}
```


##### 출처: 자료구조와 함께 배우는 알고리즘
