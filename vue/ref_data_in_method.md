# 메소드에서 DATA 값 참조하기


```javascript
const app = Vue.createApp({
    data() {
        return {
            courseGoalA: "Finish the course and learn vue!",
            courseGoalB: "master vue and build amazing apps!",
            vueLink: 'https://vuejs.org/'
        };
    },

    methods: {
        outputGoal() {
          const randomNumber = Math.random();
          if (randomNumber < 0.5) {
              return courseGoalA;
          } else {
              return "master vue!";
          }
        },
    },
});

app.mount("#user-goal");

```


```
courseGoalA는 상수나 변수가 아니기 때문에 (courseGoalA)로는 메소드에서 참조할 수 없다. (courseGoalA는 DATA의 속성값이기 때문에)
this 키워드를 사용해서 methods 내에서도 data를 사용할 수 있다. [this.courseGoalA]
```

```
data에서 반환하는 객체는 글로벌 vue instance에 병합합니다. [craeteApp]
```
