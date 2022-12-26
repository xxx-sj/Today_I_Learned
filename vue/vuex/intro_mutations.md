# intro mutations

```
vuex store에 있는 state에 직접 접근하여 값을 변경하는 것은 여전히 오류를 발생시킬 위험이 크다.
vuex에서는 mutations를 통해 state를 update하는 것을 권장한다.
component에서는 state는 직접적으로 수정하는것이 아닌 mutations를 통해 state를 변경한다.
```

```
 mutations을 통해 state를 바꾸는 것이 좋은 이유는 state를 직접적으로 바꿀 수 있지만, 
 해당 state를 바꾸는 compoennt가 여러곳이 있다면 해당 로직이 바뀔 때마다 모든 component에 접근해 값을
 변경해야 할 것이다. mutations에 정의하게 되면 mutations만 변경하면 되어 유지보수에 좋다.
```


```
mutations은 vuex를 정의한 곳에서 정의하며, mutations는 object를 value로 갖는다.
mutations에 정의한 method들은 state를 변경하는 logic이 들어가 있어야 한다. [ should ]
또한 method들은 자동적으로 인자로 current state를 가져옵니다. 해당 인자는 vuex에 의해 보장 됩니다. [is guaranteed]
해당 메소드가 트리거 될 때마다 vuex는 현재상태를 알려줍니다.
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
      state.counter = state.counter + 1; //
    },
  },
});

const app = createApp(App);
app.use(store);
app.mount('#app');
```

```
mutations 사용법은 $store의 built-in method인 commit을 사용하여 호출한다.
commit 메소드의 인자로 수행하려는 mutaions의 name을 인자로 넘긴다. name은 string으로 넘겨준다.
```

```vue
//App.vue

<template>
  <base-container title="Vuex">
    <the-counter />
    <button @click='addOne'>Add 1</button>
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
      this.$store.commit('increment'); //
    }
  }
};
</script>
```

```vue
//ChangeCounter.vue

<template>
  <button @click='addOne'>Add 1</button>
</template>

<script>
export default {
  name: 'ChangeCounter',
  methods: {
    addOne() {
      this.$store.commit('increment');
    }
  }
};
</script>
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
