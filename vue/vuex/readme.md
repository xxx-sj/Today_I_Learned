# vuex


```
vuex는 store 개념으로 어플리케이션 전반적으로 상태를 관리하게 도와주는 것이다.
사용법은 아래와 같다. 
해당 프로젝트에서 사용할 경우 [ npm install --saved vuex ] 를통해 받아서,
vue instance에 사용한다.
```


```js
//main.js

import { createApp } from 'vue';
import { createStore } from 'vuex';  //
import App from './App.vue';

const store = createStore({  //
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
  actions: {
    increment(context) {
      setTimeout(function() {
        context.commit('increment')
      }, 2000);
    },
    increase(context, payload) {
      console.log(context);
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
이해를 쉽게하기 위해서,
vuex의 option으로 전달하는 object에서 state는 component 내부의 data와 같고,
getters는 computed 와 같다고 볼 수 있다. getters는 state의 값을 직접적으로 변경하지 않고
state 값에 compute 해서 return 한다.
mutation은 state의 값을 변경하는 methods이 모여있으며, 
action과 같은경우 비동기 코드가 들어가는 곳이다.

mutation과 action의 차이는 mutation은 동기적 처리여야 하고, action은 비동기적 처리가 가능하다는 차이가 있다.
mutation이 비동기적 처리가 되었을 경우, 예측이 불가능한 상태가 될 수 있어 그것을 막기위함이고, action은 mutation으로 
commit을 통해 상태를 간접적으로 변경하지만 action을 호출하기 위한 dispatch function이 promise를 반환하기 때문에
순서적으로 제어가 가능하다.
```
