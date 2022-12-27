# intro getters

```
기존에 vuex store의 state에 직접 접근하여 값을 불러오면 만약, 다른 컴포넌트 내부에서도 같은 state를 보고 있을 경우,
해당 state logic을 모두 변경해주어야 한다. 이런것을 방지하기 위해 vuex에서 getters를 제공한다.
getters를 store에서 정의한 computed와 같다.
```

```
mutations에서와 동일하게 vuex에서 정의하며 getters 또한 object를 갖고 object의 value는 모두 method 이다.
getters에 정의한 method들은 인자로 2개를 받는데 현재 최신의 state 값, 그리고 other getters 이다.
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

  getters: {
    finalCounter(state, getters) {
      return state.counter * 2; //
    }
  }
});

const app = createApp(App);
app.use(store);
app.mount('#app');
```

```
이제 정의한 getters의 사용법은 any component에서든 사용할 수 있으며, 아래와 같이 사용한다.
```

```vue
//TheCounter.vue

<template>
  <h3>{{counter}}</h3>
</template>

<script>
export default {
  name: 'TheCounter',
  computed: {
    counter() {
      return this.$store.getters.finalCounter; //
    },
  },
};
</script>
```

```
여기서 주의해야 할 점은 getters는 method를 정의했지만, mutaions와 같이 소괄호[()]를 통해 method call [this.$store.getters.finalCounter()]
하지않고 property처럼 값만 설정해준다. [ 실행 x] 그러면, vuex가 해당 getters 메소드를 실행해준다
vuex는 getter method에 current State를 전달하면서 call하게 된다. 이렇게 getter를 사용하면 직접적으로 state에 접근하여 값을 여러곳에서 get 하는
로직을 줄이고 유지보수에 도움이 된다.
```

```
store getters 도 마찬가지로 여러개의 getter method를 가질 수 있으며, getter 메소드는 서로 의존할 수 있다. 
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

  getters: {
    finalCounter(state) {
      return state.counter * 3;
    },
    normalizedCounter(state, getters) {             //
      // const finalCounter = state.counter * 3;  //
      const finalCounter = getters.finalCounter;  //
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
normalizedCounter getter에서 볼 수 있듯이 state.counter * 3 code가 중복되었다. getter는 서로 의존할 수 있어
2번 째 인자로 getters를 받아 getters의 method를 사용할 수 있다. nomalizedCounter 에서 state는 사용되지 않는
인자여서 아래와 같이 표기할 수 있다.
```

```js
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
```

```
대신 생략을 불가한건 callback으로 getters를 받으려면 2번째 인자로 받아야하기에 생략은 불가하다.
```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
