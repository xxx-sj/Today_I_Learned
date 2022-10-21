# methods [메소드]

데이터는 하나의 함수였고, 메소드는 함수를 property로 갖는 객체이다.

```javascript

const app = Vue.createApp({
    data() {
        return {
            courseGoal: "Finish the course and learn vue!",
            vueLink: 'https://vuejs.org/'
        };
    },

    methods: {
        outputGoal() {
          const randomNumber = Math.random();
          if (randomNumber < 0.5) {
              return "learn Vue!";
          } else {
              return "master vue!";
          }
        },
    },
});

app.mount("#user-goal");

```

##### html에서 사용법
```html
 <p>{{outputGoal() }}</p>
```

