# using vuex with composition api

```
composition api에서 store를 사용하는 방법은 이전 vue-router와 비슷하게 vuex에서 제공하는 custom composition api를 사용한다.
```

```js
<script>
import { useStore } from 'vuex'
export default {
  setup() {
    const store = useStore();

    function inc() {}

    return { inc };
  },
};
</script>
```

```
userStore()를 통해 store에 대한 접근권한을 얻는다.
```

```vue
<template>
  <button @click="inc">Increment</button>
</template>

<script>
import { useStore } from 'vuex'
export default {
  setup() {
    const store = useStore();

    function inc() {
      store.dispatch('increment');
    }

    return { inc };
  },
};
</script>
```

```vue

<template>
  <h2>{{ counter }}</h2>
</template>

<script>
import { computed} from "vue";
import { useStore } from 'vuex'
export default {
  setup() {
    const counter = computed(function () {
      return store.getters.counter;
    });
    const store = useStore();

    return { counter };
  },
};
</script>
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880356#content
