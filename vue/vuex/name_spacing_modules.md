# name spacing modules

```
state이외에도 전체 모듈 또한 name space를 사용하여 로컬로 만들 수 있다. 어플리케이션이 커짐에 따라 
name 충돌이 있을 수 있다. 서로다른 module에서 같은 getter, action 이름을 사용할 수 있기 때문이다.
module을 하나의 store에 merge하기 때문이다.
```

```
사용은 간단하다. 추가할 module에 [namespaced: true] 속성을 추가해주면 된다.
해당 속성을 추가하게 되면 vuex에게 해당 모듈이 state 뿐만 아니라 전체가 store에서 분리되어야 한다 알립니다.
```

```js
//main.js

import { createApp } from 'vue';
import { createStore } from 'vuex';
import App from './App.vue';

const counterModule = {
  namespaced: true,   //
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
    testAuth(state) {
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

```
더 이상 main store에서 module을 사용할 수 없게 된다. 그렇다면 어떻게 사용할 수 있는 것인가.
사용하기 위해서는 name space를 호출하여 사용해야 한다. main store에 module을 등록할 때 사용했던 key가 
name space에 해당된다. 위와 같은 경우에서는 numbers가 name space에 해당된다.
 modules: {
  numbers: counterModule
},
이제 component에서 호출하기 위해서는 name space와 함께 사용해야 한다.
```

```vue
//FavoriteValue


<template>
  <h3>{{counter}}</h3>
  <p>We do more...</p>
</template>

<script>
export default {
  name: 'FavoriteValue',
  computed: {
    counter() {
      return this.$store.getters['numbers/normalizedCounter'];
      //return this.$store.getters.normalizedCounter;
    },
  },
};
</script>
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
        type: 'numbers/increase', //여기서도 type으로 namespace를 동일하게 넘겨준다.
        value: 10,
      })
    }
  }
};
</script>
```

```
mapGetters[mapper]를 사용할 경우 첫번 째 인자로는 name space를 2번 째 인자로는 사용할 getter method name을 배열로 넘긴다.
```

```vue
//TheCounter.vue

<template>
  <h3>{{finalCounter}}</h3>
</template>

<script>
import { mapGetters } from 'vuex'
export default {
  name: 'TheCounter',
  computed: {
    // counter() {
    //   return this.$store.getters.finalCounter;
    // },
    ...mapGetters('numbers', ['finalCounter']) //
  },
};
</script>
```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
