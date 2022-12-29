# async code with actions

```
mutions의 값은 synchronous여야 한다. 이유는 만약 하나의 mutation이 실행되었을 때 해당 값이 
비동기적으로 값이 변경되는 로직이었다면 이내 다른 mutaion이 call되었을 때 [ 아직  비동기가 끝나지 않았을 때]
기대했던 값 또는 state의 값이 다르게 나올 수 있기 때문이다.
```

```
이러한 문제를 해결해 주는 것이 vuex에서 [ Actions ]이다. actions은 비동기 코드를 사용할 수 있다.
mution을 직접적으로 call하지보다 action을 통해 mutation을 call 한다. 
[ mutation code는 동기적으로 actions은 비동기적으로도 가능하다. ]
```

```
actions 도 mutaions와 getters와 마찬가지로 methods이 모인 object이다.
action method의 인자로 context를 받는데, context는 vuex [this] 이다.
action의 mutions과 달리 async code를 사용할 수 있다.
```

```js
//main.js

import { createApp } from 'vue';
import { createStore } from 'vuex';
import App from './App.vue';

const store = createStore({
  state() {
    return {
      counter: 0
    };
  },
  mutations: {
    increment(state) {
      state.counter = state.counter + 2;
    },

    increase(state, payload) {
      state.counter = state.counter + payload.value;
    }
  },
  actions: {   //
    increment(context) {
      setTimeout(function() {
        context.commit('increment') //
      }, 2000);
    },
   increase(context, payload) {
    context.commit('increase', payload);
   }
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
    }
  }
});

const app = createApp(App);
app.use(store);
app.mount('#app');
```

```
actions을 call할 때는 dispatch를 사용한다.
```

```vue
//App.vue

<template>
  <base-container title="Vuex">
    <the-counter />
    <favorit-value />
    <button @click='addOne'>Add 10</button>
    <change-counter />
  </base-container>
</template>

<script>
import BaseContainer from './components/BaseContainer.vue';
import TheCounter from '@/components/TheCounter';
import ChangeCounter from '@/components/ChangeCounter';
import FavoritValue from '@/components/FavoritValue';

export default {
  components: {
    BaseContainer, TheCounter, ChangeCounter, FavoritValue
  },
  methods: {
    addOne() {
      // this.$store.commit('increase', {value: 10});
      /*다른 방법*/
      this.$store.dispatch({ //
        type: 'increase',
        value: 10,
      })
    }
  }
};
</script>
```

```
mutation와 비슷하게 [ mutation은 commit[ built-in ]을 사용한다.]
첫 번째 인자로는 call할 action의 method name을 2번째로는 잠재적 payload를 전달한다.
[ this.$store.dispatch('increment', {value: 10}) ] [ this.$store.dispatch({type: 'increament', value: 10}) ]
```

```
물론 action에서 mutaion에 payload를 전달하기 전에 변경할 수 있다.
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
