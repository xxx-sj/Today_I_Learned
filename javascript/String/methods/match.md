# match

```
문자열에 인자로 전달한 문자열 또는 정규식 패턴에 맞는 문자가 있다면 반환하면 없다면 null을 반환한다.
regex가 아닌 object가 전달되면 암묵적으로 Regexp로 변환된다.
매개변수를 전달하지 않고 method를 사용한다면 빈 문자열[""]이 있는 arr이 반환된다.


let string = 'love'
let patternOne = /love/     // with out any flag
let patternTwo = /love/gi   // g-means to search in the whole text, i - case insensitive

string.match(string/regex)
```


```js
let string = 'I love JavaScript. If you do not love JavaScript what else can you love.'
console.log(string.match('love'))
//["love", index: 2, input: "I love JavaScript. If you do not love JavaScript what else can you love.", groups: undefined]

let pattern = /love/gi
console.log(string.match(pattern))   // ["love", "love", "love"]
```


```js

let txt = 'In 2019, I ran 30 Days of Python. Now, in 2020 I am super exited to start this challenge'
let regEx = /\d+/

console.log(txt.match(regEx))  // ["2", "0", "1", "9", "3", "0", "2", "0", "2", "0"]
console.log(txt.match(/\d+/g)) // ["2019", "30", "2020"]
```

##### 출처: https://github.com/xxx-sj/30-Days-Of-JavaScript/blob/master/02_Day_Data_types/02_day_data_types.md 
https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String/match
