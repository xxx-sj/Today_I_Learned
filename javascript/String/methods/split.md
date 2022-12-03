# split


```
정해진 값으로 문자열을 분할한다.
인자로 값을 전달할 때는 공백, regex, 문자 등 가능하다.
```

```js
let string = '30 Days of javascript';

// 아무런 문자열로 자르지 않는다.
console.log(string.split());  // ['30 Days of javascript'];

//공백을 기준으로 문자열을 나눈다.
console.log(string.split(' ')); // ['30', 'Days', 'of', 'javascript'];

let js = 'Javascript';
//문자를 기준으로 나눈다.
console.log(js.split('')); // ['j', 'a', 'v', 'a', 's', 'c', 'r', 'i', 'p', 't'];

let numbers = 'one, two, three, four';
// 콤마를 기준으로 나눈다.
console.log(numbers.split(',')); // ['one', ' two', ' three', ' four'];

//공백 포함 콤마를 기준으로 나눈다.
console.log(numbers.split(', ')); // ['one', 'two', 'three', 'four'];
```
