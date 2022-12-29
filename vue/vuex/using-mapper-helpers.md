# using-mapper-helpers

```
vuex를 사용할 때 이 전에는 computed에서 store에 직접 접근하여 getters를 호출하였는데,
이를 좀 더 용이하게 하기위해서 vuex에서 mapper를 제공한다.
```

```vue
//TheCounter.vue

<template>
  <h3>{{finalCounter}}</h3>  //
</template>

<script>
import { mapGetters } from 'vuex'
export default {
  name: 'TheCounter',
  computed: {
    // counter() {
    //   return this.$store.getters.finalCounter;
    // },
    ...mapGetters(['finalCounter'])
  },
};
</script>
```

```
vuex에서 mapGetters를 import 받아서 computed에서 사용하게 된다. 여기서 mapGetters는 function 이다.
mapGetters()는 object를 반환한다.
spread 연산자를 통해 computed에 합칠 수 있으며, mapgetters() 의 인자로 배열을 넘기게 되는데
이 때 배열의 값을 필요로하는 getters의 method 이름이다. 또한 여러개의 getter를 호출할 수 있다. 
[ ...mapGetters(['finalCounter']) ] [ ...mapGetters([ 'getters name for use in component',  ])]
mapGetters를 통해 merge 된 getter 들은 해당 method name으로 call 할 수 있다.
```

```
mapGetters이외에도 mapActions을 통해 actions을 merge 할 수 있다.
getters와 다른점은 getters는 computed에 merge 했지만, actions는 method에 merge 한다.
```

```vue
//ChangeCounter.vue

<template>
  <button @click='increment'>Add 2</button>
  <button @click='increase({value: 11})'>Add 11</button> //
</template>

<script>
import { mapActions } from 'vuex'
export default {
  name: 'ChangeCounter',
  methods: {
    // addOne() {
    //   this.$store.dispatch('increment');
    // }
    ...mapActions(['increment', 'increase'])
  }
};
</script>
```

```
mapActions의 인자로 필요로 하는 actions name을 배열로 넘기고 만약, 인자가 [payload] 필요할 경우
element에서 call할 때 인자를 넘긴다.
다른 방법으로도 사용이 가능한데, 
```

```vue

<template>
  <button @click='inc'>Add 2</button>
  <button @click='increase({value: 11})'>Add 11</button>
</template>

<script>
import { mapActions } from 'vuex'
export default {
  name: 'ChangeCounter',
  methods: {
    // addOne() {
    //   this.$store.dispatch('increment');
    // }
    // ...mapActions(['increment', 'increase'])
    ...mapActions({
      inc: 'increment',
      increase: 'increase', //
    })
  }
};
</script>
```

```
key: value로 key는 재정의할 method name을 작성하면 된다 getters도 동일하게 사용 가능하다.

```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
