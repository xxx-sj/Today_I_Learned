# startsWith

```
인자로 전달한 문자열로 문자가 시작된다면 true 아니면 false를 반환한다.
대소문자를 구분한다.

string.startsWith(string);
```

```js
let string = 'Love is the best to in this world'

console.log(string.startsWith('Love'))   // true
console.log(string.startsWith('love'))   // false
console.log(string.startsWith('world'))  // false

let country = 'Finland'

console.log(country.startsWith('Fin'))   // true
console.log(country.startsWith('fin'))   // false
console.log(country.startsWith('land'))  //  false
```


##### 출처: https://github.com/xxx-sj/30-Days-Of-JavaScript/blob/master/02_Day_Data_types/02_day_data_types.md
