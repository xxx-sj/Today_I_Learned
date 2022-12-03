substring

```
substr과 비슷하게 문자열을 자를 때 사용한다.
인자로 2개를 받는데, 첫 번째는 자르기 시작할 인덱스, 두 번째로는 마지막으로 자를 인덱스를 전달한다.
두 번째로 전달한 인덱스 전 까지 문자열을 자른다.
```

```js
let js = 'Javascript';
// index 0부터 시작해 3 전 까지 문자열을 자른다.
console.log(js.substring(0, 3));  // 'Jav'
```
