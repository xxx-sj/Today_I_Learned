# adding an authentication page

```
login을 위한 component를 생성한다. 해당 component는 페이지 directory에 추가합니다.
또한 route로 이동할 수 있도록 route를 추가해준다. 추가 시 notFound 위에 추가해줘야 한다.
routes는 순서가 중요하기 때문에..
```

```js
//router.js

import { createRouter, createWebHistory } from 'vue-router'

import CoachDetail from '@/pages/coaches/CoachDetail';
import CoachesList from '@/pages/coaches/CoachesList';
import CoachRegistration from '@/pages/coaches/CoachRegistration';

import ContactCoach from '@/pages/requests/ContactCoach';
import RequestsReceived from '@/pages/requests/RequestsReceived';
import NotFound from '@/pages/NotFound';
import UserAuth from '@/pages/auth/UserAuth';

const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', redirect: '/coaches' },
    { path: '/coaches', component: CoachesList },
    { path: '/coaches/:id', props:true, component: CoachDetail, children: [
        { path: 'contact', component: ContactCoach }, // /coaches/c1/contact
      ] },
    { path: '/register', component: CoachRegistration },
    { path: '/requests', component: RequestsReceived },
    { path: '/auth', component: UserAuth },  //
    { path: '/:notFound(.*)', component: NotFound }, 

  ],
});

export default router;
```

```vue
// UserAuth.vue

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
      //send http request...
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
component는 기본적으로 form으로 구성되어있으며, email과 password를 받는다. 
form 전송 전 [ request to firebase ] valid를 체크하며, $data.mode를 통해 현재 상태가 
login 인지, signup인지를 check하며 swithAuthmode를 통해 mode를 변경합니다. 
login 버튼과 sign up 버튼도 computed에서 mode를 비교하여 값을 변경한다.
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880014#content
