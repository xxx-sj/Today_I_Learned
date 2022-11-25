# 생명주기 [code]

```
beforeCreate에서는 서버에 http 요청을 보내거나, 타이머 등의 작업을 수행할 수 있다.
브라우저 소스에 가서 해당 js파일에서 break point를 통해 라이프사이클을 확인할 수 있다.
```

```
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
      // this.message = this.currentUserInput;
      this.message = this.$refs.userText
    },
  },

  beforeCreate() {
    console.log("beforeCreated");
  },

  created() {
    console.log("created");
  },

  beforeMount() {
    console.log('beforeMount');
  },

  mounted() {
    console.log('mounted');
  }
  ...
});

app.mount('#app');
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide  
