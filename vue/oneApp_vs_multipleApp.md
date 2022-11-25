# one app vs multiple app

```
mount를 통해 section을 binding을 하게 되면, 독립적으로 해당 영역만 app에 인자로 전달한 data만을 사용하게 된다.
아래는 app 과 app2 를 2 개의 section으로 나누어 선언하는데 app에 선언한 data는 app2영역에서 사용할 수 없다.
```

```html
<section id="app">
  <h2>How Vue Works</h2>
  <input type="text" @input="saveInput">
  <button @click="setText">Set Text</button>
  <p>{{ message }}</p>
</section>
<section id="app2">
  {{favoriteMeal}}
</section>
```

```javascript
const app = Vue.createApp({
  data() {
    return {
      currentUserInput: '',
      message: 'Vue is great!',
    };
  },
  methods: {
    saveInput(event) {
      this.currentUserInput = event.target.value;
    },
    setText() {
      this.message = this.currentUserInput;
    },
  },
});

app.mount('#app');


const app2 = Vue.createApp({
  data() {
    return {
      favoriteMeal: "Pizza"
    };
  },
});

app2.mount("#app2")
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide
