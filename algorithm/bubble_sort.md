bubble_sort [버블 정렬]

##### 이웃한 두 요소의 대소 관계를 비교하여 교환을 반복한다.

```
오름차순 기준
끝에 있는 두 요소 부터 비교를 시작하여 서로 교환하는 작업을하며 첫 번째 요소까지 계속하면
요소 n개인 배열에서 n-1회 비교를 하게 된다. 모든 비교가 끝나고나면 가장 왼쪽에 가장 작은 요소가 오게 된다.
이런 일련의 과정(비교, 교환)을 패스(pass)라고 한다.
이 후 비교는 n-2회, n-3회 .... 패스를 k회 수행하면 요소 k개가 정렬된다. 모든 정렬이 끝나려면 n-1회의 패스가 수행되어야한다.
여기서 n-1회라는건 수행하는 패스의 횟수가 n회가 아니라 n-1회인 것은 n -1 개의 요소의 정렬이 끝나면 자연히 마지막 요소는 끝에
놓이기 때문이다. [패스 1회, 2회, 3회... n - 1회]
```

```
첫 번쨰 패스는 n - 1, 두 번째는 n - 2... 그 합계는
n-1 + n-2 + n-3 .... + 1 = n(n-1)/2
실제 요소를 교화하는 횟수는 배열의 요솟값에 더 많이 영향을 받기 때문에 교환 횟수의 평균값은 비교 횟수의 절반인 n(n -1) /4 이다.
또한 swap 메서드 안에서 값의 이동이 3회 발생하므로 이동횟수의 평균은 3n(n - 1) / 4 회이다.
```
```java

class BubbleSort {

    static void swap(int[] a, int idx1, int idx2) {
        int t = a[idx1];
        a[idx1] = a[idx2];
        a[idx2] = t;
    }

    //bubble sort
    //정렬할 배열 a, 요솟수 n
    static void bubbleSort(int[] a, int n) {
        /**
         * 뒤에서 부터 비교를 시작하므로  n - 1 [요소수 - 1]
         * j의 값은 비교 시 오른쪽에 해당하는 값  n -1 
         * j는 i보다 클 동안 for문을 탐색한다. 
         * i는 정렬이 끝난 후 마지막에 저장될 위치의 값이다.
         * 왼쪽부터 차례대로 정렬되어 저장된다.
         * a[j - 1] > a[j]는 왼쪽의 값이 오른쪽의 값보다 크다면(오름차순 정렬)
         * swap을 통해 값을 바꾼다.
         */
        for(int i = 0; i < n - 1; i++) {
            for (int j = n - 1; j > i; j--) {
                if (a[j - 1] > a[j]) {
                    swap(a, j - 1, j);
                }
            }
        }
    }

    public static void main(String[] args) {
        int[]x = new int[7];

        for (int i = 0; i < 7; i++) {
            System.out.print("x[" + i +"] : ");
            x[i] = 7 - i;
        }

        bubbleSort(x , 7);
    }
}
```

##### 알고리즘 개선-1
```
어떠한 패스에서의 요소의 교환 횟수가 0이면 더 이상 정렬할 필요가 없으므로 정렬작업을 멈춘다.
```

```java
static void bubbleSort2(int[] a, int n) {
    for(int i =0; i < n -1; i++) {
        int countOfChanged = 0; //매번 패스가 진행될 때 count를 0으로 초기화 한다.
        for (int j = n - 1; j > i; j--) {
            if (a[j - 1] > a[j]) {
                swap(a, j - 1, j);
                countOfChanged++;
            }
        }
        if (countOfChanged == 0) break; //교환이 이루어지지않았다면 종료한다.
    }
}

```

##### 알고리즘 개선-2

```
어떠한 패스에서 비교, 교환을 하다가 어떤 시점 이후에 교환이 수행되지 않는다면 그보다 앞쪽의 요소는 이미
정렬을 마친 상태라고 생각해도 좋다. 따라서 두 어떠한 패스에서 교환이 수행되지 않는다면 그 만큼 제외 후 진행해도 좋다.
```

```java

static void bubbleSort3(int[] a, int n) {
    int k = 0; //a[k]보다 앞쪽은 정렬을 마친 상태
    while(k < n -1) {
        int last = n - 1; //마지막 요소를 교환한 위치
        for (int j = n - 1; j > k; j--) {
            if (a[j - 1] > a[j]) {
                swap(a, j - 1, j);
                last = j;
            }
        }
        k = last;
    }
}
```

###### 출처: 자료구조와 함께배우는 알고리즘
