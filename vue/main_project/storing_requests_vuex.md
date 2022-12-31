# storing requests message with vuex

```
contact로 보내지는 데이터를 vuex store에 저장한다.
먼저 store 코드를 작성한다.
```

```js
// /store/requests/index.js

import mutations from '@/store/modules/requests/mutations';
import actions from '@/store/modules/requests/actions';

export default {
  namespaced: true,
  state() {
    return {
      requests: [],
    };
  },
  mutations:mutations,
  actions: actions,
}
```

```js
// store/requests/actions.js

export default {
  contactCoach(context, payload) {
    const newRequest = {
      id: new Date().toISOString(),
      coachId: payload.coachId,
      userEmail: payload.email,
      message: payload.message,
    };
    context.commit('addRequest', newRequest);
  }
};
```

```js
// store/requests/mutations.js

export default {
  addRequest(state, payload) {
    state.requests.push(payload);
  }
};
```

```
작성한 코드를 가지고 main store에 등록한다.
```

```js
//store/index.js

import { createStore } from 'vuex';

import coachesModule from './modules/coaches/index'
import requestsModule from './modules/requests/index'

const store = createStore({
  modules: {
    coaches: coachesModule,
    requests: requestsModule,
  },
  state() {
    return {
      userId: 'c3',
    };
  },
  getters: {
    userId(state) {
      return state.userId; 
    }
  }
});

export default store;
```

```vue
//ContactCoach.vue

<template>
  <form @submit.prevent='submitForm'>
    <div class='form-control'>
      <label for='email'>Your E-Mail</label>
      <input type='email' id='email' v-model.trim='email'/>
    </div>
    <div>
      <label for='message'>Message</label>
      <textarea rows='5' id='message' v-model.trim='message'/>
    </div>
    <p class='errors' v-if='!formIsValid'>Please enter a valid email and non-empty message.</p>
    <div class='actions'>
      <base-button>Send Message</base-button>
    </div>
  </form>
</template>

<script>
export default {
  name: 'ContactCoach',
  data() {
    return {
      email: '',
      message: '',
      formIsValid: true,
    };
  },
  methods: {
    submitForm() {                                        //
      this.formIsValid = true;
      if (this.email === '' || !this.email.includes("@") || this.message === '') {
        this.formIsValid = false;
        return;
      }
      this.$store.dispatch('requests/contactCoach', {                          //
        email: this.email,
        message: this.message,
        coachId: this.$route.params.id
      });
      this.$router.replace("/coaches");                                       //
    },
  },
};
</script>

<style scoped>
form {
  margin: 1rem;
  border: 1px solid #ccc;
  border-radius: 12px;
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

.errors {
  font-weight: bold;
  color: red;
}

.actions {
  text-align: center;
}
</style>
```

```
dispatch를 이용해 데이터를 전달하며, 필요한 coachId는 router가 갖고있는 id에게 가져온다.
contact 페이지가 coach list 중 해당 coach info에서 클릭하여 넘어오기에 url이 [ http://localhost:8080/coaches/c1/contact ]
전달받아서 가져올 수 있다.
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
