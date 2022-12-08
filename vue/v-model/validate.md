# 유효성 검사

```
v-model와 blur event를 통해 유효성검사를 할 수 있다.
blur event는 focus 가 out될 때 발생되며, 발생된 이벤트 내부에서는 input에 입력된 값이 있느지 체크한다.
```

```vue

...

<div class="form-control" :class='{invalid: userNameValidity === "invalid"}'> //
      <label for="user-name">Your Name</label>
      <input id="user-name" name="user-name" type="text" v-model='userName' @blur='validateInput'/>
      <p v-if='userNameValidity === "invalid"'>Please enter a valid name!</p>
</div>

 data() {
    return {
      userName: '',
      userAge: null,
      referrer: 'wom',
      interest: [],
      how: null,
      confirm: false,
      userNameValidity: 'pending',
    };
  },
  
  methods: {
    validateInput() {
      if (this.userName.trim() === '') {
        this.userNameValidity = 'invalid';
      } else {
        this.userNameValidity = 'valid';
      }
    },
  }
```
