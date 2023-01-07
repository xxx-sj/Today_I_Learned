# add login action and flow

```
regist와 마찬가지로 fire-base의 api를 이용하여 login 한다.
```

```js
//auth/actions.js


export default { 
  async login(context, payload) {//
    const response = await fetch(`https://identitytoolkit.googleapis.com/v1/accounts:signInWithPassword?key=AIzaSyAJLkNz9pbIzgj89yJwk3SXJW3MUuTp5L8`, {
      method: 'POST',
      body: JSON.stringify({
        email: payload.email,
        password: payload.password,
        returnSecureToken: true,
      })
    }); //

    const responseData = await response.json();

    if (!response.ok) {
      console.log({responseData});
      const error = new Error(responseData.message || 'Failed to authenticate. check your login data');
      throw error;
    }

    console.log({responseData});
    context.commit('setUser', {
      token: responseData.idToken,
      userid: responseData.localId,
      tokenExpiration: responseData.expiresIn,//
    })
  }, 
  
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
      const error = new Error(responseData.message || 'Failed to authenticate. check your login data');
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
login logic은 regist와 비슷하며 fetch로 전달하는 url정도만 다르다. 이제 UserAuth component 내에서 입력한 
email와 password를 가지고 로그인을 시도한다.
```

```vue
//UserAuth.vue

<template>
  <div>
    <base-dialog :show='!!error' title='An error occurred' @close='handleError'>
      <p>{{ error }}</p>
    </base-dialog>
    <base-dialog :show='isLoading' title='Authenticating...' fixed>
      <p>Authenticating...</p>
      <base-spinner></base-spinner>
    </base-dialog>
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
  </div>
</template>

<script>
import BaseButton from '@/components/UI/BaseButton';
import BaseSpinner from '@/components/UI/BaseSpinner';
import BaseDialog from '@/components/UI/BaseDialog';
export default {
  name: 'UserAuth',
  components: { BaseDialog, BaseSpinner, BaseButton },
  data() {
    return {
      email: '',
      password: '',
      formIsValid: true,
      mode: 'login',
      isLoading: false,
      error: null,
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
    async submitForm() {
      this.formIsValid = true;
      if (this.email === '' || !this.email.includes('@') || this.password.length < 6) {
        this.formIsValid = false;
        return;
      }

      this.isLoading = true;

      const actionPayload = {                                                //
        email: this.email,                                                   //
        password: this.password,                                               //
      }

      try {
        if (this.mode === 'login') {
          await this.$store.dispatch('auth/login', actionPayload)             //
        } else {
          await this.$store.dispatch('auth/signup', actionPayload);
        }
      } catch (err) {
        this.error = err.message || 'Failed to authenticate, try later.';
      }


      this.isLoading = false;
    },
    switchAuthMode() {
      if (this.mode === 'login') {
        this.mode = 'signup';
      } else {
        this.mode = 'login'
      }
    },

    handleError() {
      this.error = null;
    }
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


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880026#content
