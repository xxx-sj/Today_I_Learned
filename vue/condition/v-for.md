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

##### 출처 : https://www.udemy.com/course/vuejs-2-the-complete-guide
