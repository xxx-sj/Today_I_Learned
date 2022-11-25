# ref [reference]

```
ref attribute는 html의 기본 속성이 아니다. key와 마찬가지로 vue에서 제공한다. 따라서 브라우저는 알 수 없다.
ref는 어떠한 element에도 사용할 수 있다. vue는 참조를 감지하고 내부적으로 저장한다. 모든 vue 제공 속성은 $ [달러] 기호로
시작한다. $ref, $data ...
this.$refs object는 key와 value로 이루어져 있으며, 해당 key값은 element에 작성한 값이 key가 된다. 
ex) ref="useeText" -> this.$refs.userText
해당 value는 단순히 DOM object를 가리킨다. 따라서 해당 값이 access 될 때 vanilla javascript도 사용할 수 있다.

```

```html
<section id="app">
  <h2>How Vue Works</h2>
  <input type="text" ref="userText">
  <button @click="setText">Set Text</button>
  <p>{{ message }}</p>
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
      // this.message = this.currentUserInput;
      this.message = this.$refs.userText.value;
    },
  },
});

app.mount('#app');
```

```
console.dir(this.$refs.userText)를 호출하면 해당 element의 attribute를 확인할 수 있다.
```

```
위 예시에서는 사용자가 입력한 값에 대해 validate를 체크할 때 ref를 사용하며, 어떠한 이벤트 발생 시 
해당 값에대해 ref를 통해 value를 가져오는것을 하였다. v-model을 쓰거나 @input을 사용할 수 있지만 [v-model은 :value + @input]
매번 입력 시 마다 발생하므로, v-model 이외에 어떠한 이벤트 발생 시 ref를 통해 value를 가져오는 것을 예시로 보여준다.
```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide
