# event_binding [이벤트 바인딩]

```
start-vuew에서 작성한 코드에서 인자로 넘기는 객체에 methods라는 property를 추가한다.
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
});
```
```
이 후 index.html 파일에서 button에 이벤트 바인딩을한다.
v-on:[event_name]=[method_name] or @[method_name]
```

```javascript
<button v-on:click="addGoal">Add Goal</button>
```

