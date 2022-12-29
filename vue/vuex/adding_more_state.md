# adding more state

```
로그인을 예시로 state와 getters 그리고 action을 사용해본다.
시나리오는 아래와 같다

로그인 버튼과 로그아웃 버튼이 있다.
로그인이 되어있지 않을 때는 App.vue에서 상단 container가 보이지 않게, login 상태에서는 보이게 하는 것이다.
```

```
먼저 로그인 상태를 추가해주고, 해당 상태를 변경할 수 있는 mutation와 action을 추가한다
[ store의 값 변경은 action -> mutation -> state 순으로 일어난다. ]
또한, 로그인 상태를 확인 할 수 있는 getter 메소드도 추가한다.
```

```js
//main.js

import { createApp } from 'vue';
import { createStore } from 'vuex';
import App from './App.vue';

const store = createStore({
  state() {
    return {
      counter: 0,
      isLoggedIn: false,
    };
  },
  mutations: {
    increment(state) {
      state.counter = state.counter + 2;
    },

    increase(state, payload) {
      state.counter = state.counter + payload.value;
    },

    setAuth(state, payload) {
      state.isLoggedIn = payload.isAuth; //
    },
  },
  actions: {
    increment(context) {
      setTimeout(function() {
        context.commit('increment')
      }, 2000);
    },
    increase(context, payload) {
      console.log(context);
      context.commit('increase', payload);
    },

    login(context) {
      context.commit('setAuth', {isAuth: true});  //
    },
    logout(context) {
      context.commit('setAuth', {isAuth: false});  //
    },
  },

  getters: {
    finalCounter(state) {
      return state.counter * 3;
    },
    normalizedCounter(_, getters) {
      // const finalCounter = state.counter * 3;
      const finalCounter = getters.finalCounter;
      if (finalCounter < 0) {
        return 0;
      }
      if (finalCounter > 100) {
        return 100;
      }
      return finalCounter;
    },

    userIsAuthenticated(state) {  //
      return state.isLoggedIn;
    }
  }
});

const app = createApp(App);
app.use(store);
app.mount('#app');
```


```vue
//UserAuth.vue

<template>
  <button @click='login' v-if='!isAuth'>Login</button>
  <button @click='logout' v-else>Logout</button>
</template>

<script>
export default {
  name: 'UserAuth',
  methods: {
    login() {
      this.$store.dispatch('login'); //
    },
    logout() {
      this.$store.dispatch('logout'); //
    },
  },
  computed: {
    isAuth() {
      return this.$store.getters.userIsAuthenticated //
    }
  }
};
</script>
```

```
login 버튼이 있는 곳이다. mapActions을 써도 되고, 따로 작성하여도 된다.
```

```vue
//App.vue

<template>
  <base-container title="Vuex" v-if='isAuth'>
    <the-counter />
    <favorite-value />
    <button @click='addOne'>Add 10</button>
    <change-counter />
  </base-container>
  <base-container title='Auth'>
    <user-auth />
  </base-container>
</template>

<script>
import BaseContainer from './components/BaseContainer.vue';
import TheCounter from '@/components/TheCounter';
import ChangeCounter from '@/components/ChangeCounter';
import FavoriteValue from '@/components/FavoriteValue';
import userAuth from '@/components/UserAuth';

export default {
  components: {
    BaseContainer, TheCounter, ChangeCounter, FavoriteValue, userAuth
  },
  computed: {
    isAuth() {
      return this.$store.getters.userIsAuthenticated
    }
  },
  methods: {
    addOne() {
      // this.$store.commit('increase', {value: 10});
      /*다른 방법*/
      this.$store.dispatch({
        type: 'increase',
        value: 10,
      })
    }
  }
};
</script>
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
