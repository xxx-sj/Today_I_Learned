# local module state

```
store module을 merge하면 same level로 merge된다. 이러한 상태를 또한 변경 가능하다.
기본적으로 merge할 때 변경되는 부분이 있는데, 그것이 state이다.
현재 나뉘어져 있는 counter module에서 getters로  isAuth [root state] 를 호출하면 찾을 수 없다.
state는 사실 local로 취급되기 때문이다. mutations, actions and getters는 global로 되어있다.
```

```
해결할 수 있는 방법이 있는데, getters에서 이전에 인자로 받던, [ state, getters ] 이외에도 [ rootSate, rootGetters ]를 
받을 수 있다. 이러한 방법은 특정 모듈에서 사용할 때 사용 할 수 있다.
```

```js
//main.js
import { createApp } from 'vue';
import { createStore } from 'vuex';
import App from './App.vue';

const counterModule = {
  state() {
    return {
      counter: 0,
    }
  },
  mutations: {
    increment(state) {
      state.counter = state.counter + 2;
    },

    increase(state, payload) {
      console.log(state);
      state.counter = state.counter + payload.value;
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
  },
  getters: {
    testAuth(state, getters, rootState, rootGetters) {  //
      return state.isLoggedIn;
    },
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
  },
}

const store = createStore({
  modules: {
    numbers: counterModule
  },
  state() {
    return {
      isLoggedIn: false,
    };
  },
  mutations: {
    setAuth(state, payload) {
      state.isLoggedIn = payload.isAuth;
    },
  },
  actions: {
    login(context) {
      context.commit('setAuth', {isAuth: true});
    },
    logout(context) {
      context.commit('setAuth', {isAuth: false});
    },
  },

  getters: {
    userIsAuthenticated(state) {
      return state.isLoggedIn;
    }
  }
});

const app = createApp(App);
app.use(store);
app.mount('#app');

```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
