# 데이터 표시하기

```
js파일에서 작성한 data 값은 html에서 {{[data]}}을 통해 표현할 수 있다. (참조)
```


```javasciprt
const app = Vue.createApp({
    data() {
        return {
            courseGoal: "Finish the course and learn vue!"
        };
    }
});

app.mount("#user-goal");
```

```html
<p>{{courseGoal}}</p>
```

```
vue가 제어하지않는 부분에서는 데이터가 표시되지않는다.
```

