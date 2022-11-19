# 반복문

```
javascript에서 사용하는 for문과 유사하게 vue에서도 for문[반복문]을 제공한다.
```

```html
<li v-for="goal in goals">Goal {{ goal }}</li>
```

```
범위는 태그와 태그 내부로 한정된다.
v-for는 dom을 전체 update하는 것이 아니라 dom 에 element를 추가하는 방식으로 동작한다.
```

```javascript
data() {
    return {
      enteredGoalValue: '',
      goals: [],
    };
  },
```

```
v-for에서 index 도 가져올 수 있다 [ v-for="(goal, index) in goals" ]
```

```
v-for 반복문은 배열 이외에도 객체를 반복해서 출력할 수 있다.
```
```html
<li v-for="value in {name: 'max', age: '31'}">{{ value }}</li> 
// max
// 31
```
```
또한 index를 가져왔듯이, 객체에서 key값을 가져올 수 있다
```
```html
<li v-for="(value, key) in {name: 'max', age: '31'}">{{key}} : {{ value }}</li> 
//name : max
//age: 31
```
```
추가적으로, index 또한 가져올 수 있다.
```

```html
<li v-for="(value, key, index) in {name: 'max', age: '31'}">{{key}} : {{ value }} - {{ index }}</li> 
//name : max - 0
//age: 31 - 1
```

##### 출처 : https://www.udemy.com/course/vuejs-2-the-complete-guide
