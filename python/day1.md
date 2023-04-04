# python

### print

```python
import io
print("")
```
* * *

### 리터럴
```python
"" / '' / \`\`\` \`\`\`
```
### 리터럴 인덱싱
```python
str = "인덱싱 테스트"
str[0 : 1] // "인"
```
* * *

### 리스트
```python
list = [] or list = list()
list = ["a", "b", "c"]
```

#### 리스트 메소드
```python
len(list) = 3
list.insert(2, "b-1")  // ["a", "b", "b-1", "c"]  // 중간삽입
list.append("d") // 맨 마지막 인덱스에 추가

//리스트 정렬
list = [1, 3, 4, 2, 6]
sorted(list, reverse=True)
// 6,4,3,2,1
```


##### 출처: http://bigdata.dongguk.ac.kr/lectures/Python/_book/%ED%8C%8C%EC%9D%B4%EC%8D%AC%EC%9D%98-%EA%B8%B0%EC%B4%88.html
