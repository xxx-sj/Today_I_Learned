# passing data to mutations with payloads

```
vuex 의 mutaions에 payload를 전달할 수 있다.
payload를 통해 state 값을 동적으로 변경할 수 있다.
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
      state.counter = state.counter + 1;
    },

    increase(state, payload) {
      state.counter = state.counter + payload.value; //
    }
  },
});

const app = createApp(App);
app.use(store);
app.mount('#app');
```

```
payload는 string, object, number 든 모두 될 수 있다. 해당 mutation에 맞게 설정하면 된다.
```

```vue
//App.vue

<template>
  <base-container title="Vuex">
    <the-counter />
    <button @click='addOne'>Add 10</button>
    <change-counter />
  </base-container>
</template>

<script>
import BaseContainer from './components/BaseContainer.vue';
import TheCounter from '@/components/TheCounter';
import ChangeCounter from '@/components/ChangeCounter';

export default {
  components: {
    BaseContainer, TheCounter, ChangeCounter
  },
  methods: {
    addOne() {
      this.$store.commit('increase', {value: 10}); // 
    }
  }
};
</script>
```

```
기대하는 값이 obejct의 value 이기때문에 payload에 object에 value로 값을 넘긴다.
또한 다른방법으로 mutaions을 call할 수 있는데 아래와 같다.
```

```js

<script>
import BaseContainer from './components/BaseContainer.vue';
import TheCounter from '@/components/TheCounter';
import ChangeCounter from '@/components/ChangeCounter';

export default {
  components: {
    BaseContainer, TheCounter, ChangeCounter
  },
  methods: {
    addOne() {
      // this.$store.commit('increase', {value: 10});
      /*다른 방법*/
      this.$store.commit({
        type: 'increase',   // call 할 mutaion name
        value: 10,  //payload에 전달한 값
      })
    }
  }
};
</script>
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
