# 뷰 시작하기

- index.html 에 script 추가하기
```javascript
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
```
- 추가로 작성한 app.js 파일에서 전역객체 Vue 만들기
```javascript
Vue.createApp({
    data() {
        return {
            goals: [],
            enteredValue: '',
        };
    },
});
```
```
createApp에 객체를 인자로 넘기면서 data 메소드에 return 값으로 
객체를 반환한다.
```


```
작성한 코드는 html 중 어디에 적용되야 하는지를 알려줘야 한다.
mount에 인자로 문자열을 넘길 때는 css선택자로 dom요소를 선택한다.
```

```javascript
Vue.createApp({
    data() {
        return {
            goals: [],
            enteredValue: '',
        };
    },

   methods: {
     addGoal() {
         this.goals.push(this.enteredValue);
     }
   },
}).mount("#app");

```
