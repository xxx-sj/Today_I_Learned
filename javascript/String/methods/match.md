# match

```
문자열에 인자로 전달한 문자열 또는 정규식 패턴에 맞는 문자가 있다면 반환하면 없다면 null을 반환한다.

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
