# castring

```
데이터의 유형을 다른 데이터 유형으로 변환한다.
변환 데이터의 유형이 맞지 않으면 오류를 반환한다.
```

### 문자열에서 숫자로 변환
- parseInt()
- Number()
- +
- parseFloat()

```js
let num = '10';
let numInt = parseInt(num);
console.log(numInt); // 10

let num2 = '20.2'
let numInt2 = parseFloat(num2);
console.log(numInt2); // 20.2
```

```js
let num = '10'
let numInt = Number(num)
console.log(numInt) // 10

let num2 = '20.2'
let numInt2 = Number(num2);
console.log(numInt2); // 20.2
```

```js
let num = '10'
let numInt = +num
console.log(numInt) // 10

let num2 = '20.2'
let numInt2 = +num2;
console.log(numInt2); // 20.2
```

```
float의 소숫점을 잘라내고 싶다면 parseInt를 통해 없앨 수 있다.
```

```js
let num = 20.2
let numInt = parseInt(20.2);
console.log(numInt); // 20
```
