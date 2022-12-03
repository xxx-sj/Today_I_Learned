# endsWith

```
인자로 전달한 값으로 끝난다면 true 아니면 false를 반환한다.
대소문자를 구분한다.
```

```js

let string = 'Love is the most powerful feeling in the world'

console.log(string.endsWith('world'))         // true
console.log(string.endsWith('love'))          // false
console.log(string.endsWith('in the world')) // true

let country = 'Finland'

console.log(country.endsWith('land'))         // true
console.log(country.endsWith('fin'))          // false
console.log(country.endsWith('Fin'))          //  false
```

##### 출처: https://github.com/xxx-sj/30-Days-Of-JavaScript/blob/master/02_Day_Data_types/02_day_data_types.md
