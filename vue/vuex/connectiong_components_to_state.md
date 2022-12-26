# connectiong components to state

```
vuex store에 등록한 값은 application 내 어느 component에서도 접근이 가능하다.
또한 component 내 method 에서도 state에 접근하여 값을 변경할 수 있다.
```

```vue
//App.vue

<template>
  <base-container title="Vuex">
    <the-counter />
    <button @click='addOne'>Add 1</button>
  </base-container>
</template>

<script>
import BaseContainer from './components/BaseContainer.vue';
import TheCounter from '@/components/TheCounter';

export default {
  components: {
    BaseContainer, TheCounter
  },
  methods: {
    addOne() {
      this.$store.state.counter++;
    }
  }
};
</script>
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
      return this.$store.state.counter;
    },
  },
};
</script>
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
