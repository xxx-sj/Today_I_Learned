# organize store with modules

```
vuex [store] 을 여러개의 모듈로 분할할 수 있다. 하나의 어플리케이션은 하나의 스토어만 보유할 수 있지만,
여러개의 모듈로 구성될 수 있다. 여러개의 모듈이 없다면 (이 전에 사용하던 방법) 해당 모듈을 ROOT-MODULE, ROOT-STATE
라고 부르기도 한다. [ const store = createStore({}) ]
```

```
module을 만들 때는 store를 새로 만드는것이 아닌 하나의 object를 생성하는 것이다.
이 object는 store의 option으로 전달하는 [저장소를 만들기 위해] object와 동일하다.
동일하게, state, actions, mutations, getters를 property로 갖는다.
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

```
새로 생성한 module을 merge하는 방법은 createStore의 option으로 motuldes를 추가하면 된다.
[ root object에 추가하기 ]
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
    numbers: counterModule   //
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

```
module의 key값은 임의로 지을 수 있다.
```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
