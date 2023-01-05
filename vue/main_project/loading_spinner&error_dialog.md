# add loading spinner and error dialog

```
login 화면에서 ux를 향상시키기 위해 회원가입 시 spinner를 보이게 추가해주었다.
```

```
<template>
  <div>
    <base-dialog :show='!!error' title='An error occurred' @close='handleError'>  //
      <p>{{ error }}</p>   //
    </base-dialog>       //
    <base-dialog :show='isLoading' title='Authenticating...' fixed> //
      <p>Authenticating...</p> // 
      <base-spinner></base-spinner>  //
    </base-dialog>         //
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

      this.isLoading = true;                     //

      try {
        if (this.mode === 'login') {              //
          //...
        } else { 
          await this.$store.dispatch('auth/signup', {    //
            email: this.email,                          //
            password: this.password,                    //
          });
        }
      } catch (err) {                                    //
        this.error = err.message || 'Failed to authenticate, try later.'; //
      }


      this.isLoading = false;                //
    },
    switchAuthMode() { 
      if (this.mode === 'login') {
        this.mode = 'signup';
      } else {
        this.mode = 'login'
      }
    },

    handleError() {  ///
      this.error = null;      //
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

```
submitForm에서 vaild가 통과되면 isLoading을 true를 변경하면서 spinner를 보여주게 된다 
try/catch문으로 signup을 감싸서 에러가 발생한다면 error dialog를 보여주게 설계한다. 
또한 async/await으로 isLoading이 dispath가 끝나기 전에 동작하지 않게 막는다.
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880026#overview
