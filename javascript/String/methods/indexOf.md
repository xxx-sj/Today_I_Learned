# indexOf

```
인자로 넘긴 문자열이 포함되어있는 문자열의 인덱스 값을 반환한다.
해당하는 string이 없으면 -1 을 반환한다.

string.indexOf(string)
```

```js
let string = 'it is javascript';

console.log(string.indexOf('i')); // 0
console.log(string.indexOf('it')); // 0
console.log(string.indexOf('is')); // 3
console.log(string.indexOf('c')); // 11
console.log(string.indexOf('javascript')); //6
console.log(string.indexOf('java')); //6
```
