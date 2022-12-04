# 배열

```
변수와 달리 여러 값을 저장할 수 있다. 배열의 각 값에는 인덱스가 있고 각 인덱스에는 메모리 주소의 참조가 있다.
```

**빈 배열 만들기**
```
let 대신 const 를 쓰는 이유는 배열을 저장한 변수의 item이 변해도 배열의 주소는 같기 때문이다.
```

```js
const arr = Array();
const arr = new Array();
const arr = [];
```

```
배열을 초기화할 때 기본값을 넣어 초기화 할 수 있다.
```

```js
const numbers = [1,2,3,3,4,5];
const alpa = ['a', 'b', 'c', 'd', 'e'];
```

```
배열에는 같은 타입의 데이터 이외에도 다른 타입의 데이터도 함께 넣을 수 있다.
```

```js
const arr = [1,2,3, 'a', 'b', [1,2], {'a': 'a', 'b': 'b'}];
```

**배열 값에 접근하기**
```
배열에 저장된 데이터에 접근 시 index 값으로 접근한다.
```

```js
const arr = [1,2,3,4,5,6];

console.log(arr[0]) // 1;

arr[2] = 10;
console.log(arr) // [1, 2, 10, 4, 5, 6]
```

```
배열을 초기화할 때 초기 배열의 크기를 정해서 초기화 할 수 있다. 
```

```js
const arr = Array(8); // empty x 8
```

**fill**
```
모든 배열 요소를 정적 값으로 채운다.
```

```js
const arr = Array(8).fill(2); // [2,2,2,2,2,2,2,2];
const arr = Array(4).fill('x') // ['x','x','x','x'];
```

***concat***
```
두 배열을 합칠 때 사용한다.
```

```js
const firstList = [1, 2, 3]
const secondList = [4, 5, 6];
const concatList = firstList.concat(secondList);
console.log(concatList) // [1,2,3,4,5,6];
```

***indexOf***
```
인자로 전달하는 값이 배열에 존재하는지 확인한다 존재한다면 해당 index 값을 없으면 -1을 반환한다.
[배열의 처음부터 순회해서 찾는다.]
```

```js
const numbers = [1, 2, 3, 4, 1];

console.log(numbers.indexOf(5)); // -1
console.log(numbers.indexOf(1)); // 0; 
```

**lastIndexOf**
```
인자로 전달하는 값의 마지막 index값을 제공한다. 없으면 -1을 반환한다.
[ 배열의 끝 부터 순회해서 찾는다. ]
```

```js
const numbers = [1, 2, 3, 4, 1, 2]
console.log(numbers.lastIndexOf(2)); //5;
console.log(numbers.lastIndexOf(1)); // 4;
console.log(numbers.lastIndexOf(4)); // 3;
console.log(numbers.lastIndexOf(10)); // -1;
```

**includes**
```
인자로 전달하는 값이 배열에 존해마녀 true 없다면 false를 반환한다.
```

```js
const numbers = [1, 2, 3, 4, 5];

console.log(numbers.includes(5)) // true;
console.log(numbers.includes(1)) // true;
console.log(numbers.includes(10)) // false;
```

**Array.isArray**
```
데이터의 유형이 array인지 확인할 때 사용한다. bool을 반환한다.
array는 기본적으로 object 이다.
```

```js
const arr = [];
Array.isArray(arr); // true;
```

**toString()**
```
배열을 문자열로 변환하여 출력한다.
```

```js
const arr  = [1, 2, 3, 4];
console.log(arr.toString()); // 1, 2, 3, 4
```

**join**
```
배열의 요소를 인자로 전달한 값으로 연결하여 문자열로 반환한다.
기본적으로 인자의 값이 없으면 , 를 사용하게 된다.
```

```js
const numbers = [1, 2, 3, 4, 5];
console.log(numbers.join()); // 1,2,3,4,5
console.log(numbers.join("#")); // 1#2#3#4#5
console.log(numbers.join('')); //12345
```

**slice**
```
배열의 범위 내에서 항목들으 잘라낸다. 시작 위치와 종료 위치를 인자로 넘긴다. [ 종료 위치는 포함되지 않는다.]
```

```js
const numbers = [1, 2, 3, 4, 5];
console.log(numbers.slice()); // [1, 2, 3, 4, 5]
console.log(numbers.slice(0)); // [1, 2, 3, 4, 5]
console.log(numbers.slice(0, numbers.length)); // [1, 2, 3, 4, 5];
console.log(numbers.slice(0 , 2)); // [1, 2];
```

**splice()**
```
인자로 전달하는 시작위치 인덱스 와 자를 data 의 수를 넘긴다.

array.splice(1, 3); // index 1부터 3개를 자른다.
array.splice(startIndex, deleteCount, item); // 자른만큼 item으로 대체한다.
```

```js
const numbers = [1, 2, 3, 4, 5];
console.log(numbers.splice()); // [];
console.log(numbers.splice(0, 2)); // [1, 2]; 
```

**push**
```
배열 끝에 인자로 전달하는 항목을 추가한다.
```

```js
const arr = [1, 2, 3];
arr.push(4);
console.log(arr); // [1, 2, 3, 4]
```

**pop**
```
배열의 마지막 항목을 제거한다.
array.pop();
```

```js
const numbers = [1, 2, 3, 4, 5];
numbers.pop(); // [5];
console.log(numbers); // [1, 2, 3, 4];
```

**shift**
```
배열 시작 부분에서 요소를 하나 제거한다.
```

```js
const numbers = [1, 2, 3, 4];
numbers.shift(); // [1];
console.log(numbers); // [2, 3, 4]
```

**unshift**
```
배열의 시작 부분에 요소를 추가한다.
```

```js
const numbers = [1, 2, 3, 4, 5];
numbers.unshift(0);
console.log(numbers)// [0, 1, 2, 3, 4, 5];
```

**reverse**
```
배열의 요소 순서를 반대로 바꾼다.
```

```js
const numbers = [1, 2, 3, 4];
numbers.reverse();
console.log(numbers); // [4, 3, 2, 1];
```

**sort**
```
배열 요소를 오름차순으로 정렬한다. sort의 인자로는 콜백 함수를 전달한다.
```

```js
const webTechs = [
  'HTML',
  'CSS',
  'JavaScript',
  'React',
  'Redux',
  'Node',
  'MongoDB'
]

webTechs.sort()
console.log(webTechs)
```

