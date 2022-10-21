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


```
html 속성값에 데이터를 binding 할 수 있다.
```


```html
<p>Learn more <a v-bind:href="vueLink">about vue.</a></p>
```


```
{{}}는 태그를 열고 닫는 사이에서 사용할 수 있다.
속성에 동적으로 값을 바인딩 싶다면,  prefix로 v-bind: 를 추가하고 값으로 전달하려는 data를 넘기면 된다.
v-bind:[attr]="[data]"
```
