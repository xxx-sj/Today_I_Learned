# add signup action flow

```
fire-base 주소
```
(fire-base-rest-api)[https://firebase.google.com/docs/reference/rest/auth#section-create-email-password]


```
auth의 actions을 작성한다 fire-base와 연동
```

```js
//actions.js

export default {
  login() {},
  async signup(context, payload) {
    const response = await fetch(`https://identitytoolkit.googleapis.com/v1/accounts:signUp?key=AIzaSyAJLkNz9pbIzgj89yJwk3SXJW3MUuTp5L8`, {
      method: 'POST',
      body: JSON.stringify({
        email: payload.email,
        password: payload.password,
        returnSecureToken: true,
      })
    });

    const responseData = await response.json();

    if (!response.ok) {
      console.log({responseData});
      const error = new Error(responseData.message || 'Failed to authenticate.');
      throw error;
    }

    console.log({responseData});
    context.commit('setUser', {
      token: responseData.idToken,
      userid: responseData.localId,
      tokenExpiration: responseData.expiresIn,
    })
  },
}

```

```
fetch를 통해 fire-base signup rest-api를 사용하며, method로는 post를 사용한다. 
전달할 값으로는 email, password 그리고 returnSecureToken [ id와 리프레시 토큰을 리턴할지 말지를 결정 (should always be true) ]이다.
fetch 메소드는 항상 promise를 반환하기 때문에 async / await을 통해 작성할 수 있다.
작성 시 response error가 발생하면 error message를 작성하여 throw 한다.
```

```js
//mutations.js

export default {
  setUser(state, payload) {
    state.token = payload.token;
    state.userId = payload.userId;
    state.tokenExpiration = payload.tokenExpiration;
  }
};
```

```js
//index.js


import mutations from './mutations'
import actions from './actions'
import getters from './getters'
export default {
  namespaced: true,
  state() {
    return {
      userId: null,
      token: null,
      tokenExpiration: null,
    };
  },
  mutations,
  getters,
  actions,
}
```

```
actions을 통해 전달받은 데이터는 mutations에서 state에 저장하게 된다. userAuth에 붙여 사용하는 것으로 이번 md는 마무리
```

```vue
//UserAuth.vue

<template>
  <base-card>
      <form @submit.prevent='submitForm'>
        <div class='form-control'>
          <label for='email'>E-Mail</label>
          <input type='email' id='email' v-model.trim='email'>
        </div>
        <div class='form-control'>
          <label for='password'>Password</label>
          <input type='password' id='password' v-model.trim='password'>
        </div>
        <p v-if='!formIsValid'>Please enter a valid email and password (must be at least 6 characters long).</p>
        <base-button>{{ submitButtonCaption }}</base-button>
        <base-button type='button' mode='flat' @click='switchAuthMode'>{{ switchModeButtonCaption }}</base-button>
      </form>
  </base-card>
</template>

<script>
import BaseButton from '@/components/UI/BaseButton';
export default {
  name: 'UserAuth',
  components: { BaseButton },
  data() {
    return {
      email: '',
      password: '',
      formIsValid: true,
      mode: 'login',
    };
  },
  computed: {
    submitButtonCaption() {
      if (this.mode === 'login') {
        return 'Login';
      } else {
        return 'Signup';
      }
    },
    switchModeButtonCaption() {
      if (this.mode === 'login') {
        return 'Signup instead';
      } else {
        return 'Login instead';
      }
    },
  },
  methods: {
    submitForm() {
      this.formIsValid = true;
      if (this.email === '' || !this.email.includes('@') || this.password.length < 6) {
        this.formIsValid = false;
        return;
      }

      if (this.mode === 'login') {              //
        //...
      } else {
        this.$store.dispatch('auth/signup', {           //
          email: this.email,                       //
          password: this.password,            //
        });
      }

    },
    switchAuthMode() {
      if (this.mode === 'login') {
        this.mode = 'signup';
      } else {
        this.mode = 'login'
      }
    },
  }
};
</script>

<style scoped>
form {
  margin: 1rem;
  padding: 1rem;
}

.form-control {
  margin: 0.5rem 0;
}

label {
  font-weight: bold;
  margin-bottom: 0.5rem;
  display: block;
}

input,
textarea {
  display: block;
  width: 100%;
  font: inherit;
  border: 1px solid #ccc;
  padding: 0.15rem;
}

input:focus,
textarea:focus {
  border-color: #3d008d;
  background-color: #faf6ff;
  outline: none;
}
</style>
```

```
등록을 하고나면 fire-base authentication tab에 email와 password로 저장된 user를 볼 수 있다.
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880020#content
