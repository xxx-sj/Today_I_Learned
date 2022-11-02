# v-model 데이터 바인딩

stat-vue에서 작성한 코드

```javascript
[app.js]
Vue.createApp({
    data() {
        return {
            goals: [],
            enteredValue: '',
        };
    },
});
```


enteredValue와 html element 와 데이터 바인딩을한다.


```javascript
<input type="text" id="goal" v-model="enteredValue"/>
```
