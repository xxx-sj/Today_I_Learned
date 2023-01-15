# lifecycle hooks in composition api

### readme 참고

```
vue에서 create 관련 life hook을 제외한 다른 lifecycle hook을 import 받을 수 있다.
```

```vue
//UserData.vue

<template>
  <div>
    <h2> {{ userName }}</h2>
    <h3> {{ age }}</h3>
  </div>
</template>

<script>
import {
  computed,
  inject,
  onBeforeMount,
  onMounted,
  onBeforeUpdate,
  onUpdated,
  onBeforeUnmount,
  onUnmounted
} from 'vue'
export default {
  name: "UserData",
  props: ['firstName', 'lastName'],
  setup(props, context) {
    const uName = computed(function() {
      return props.firstName + '' + props.lastName;
    });

    const age = inject('userAge')

    console.log(context)

    onBeforeMount(function() {
      console.log('onBeforeMount')
    });

    onMounted(function() {
      console.log('onMounted')
    });

    onBeforeUpdate(function() {
      console.log('onBeforeUpdate')
    });
    onUpdated(function() {
      console.log('onUpdated')
    });
    onBeforeUnmount(function() {
      console.log('onBeforeUnmount')
    });
    onUnmounted(function() {
      console.log('onUnmounted')
    })

    // context.emit('save-data', 1); // this.$emit('save-data', 1);

    return { userName: uName, age: age };
  },
  // computed: {
  //   userName() {
  //     return this.firstName + " " + this.lastName
  //   }
  // },
}
</script>
```

```
import 받은 lifecylce hook들은 setup 메소드에서 위와 같이 function을 정의하여 인자로 넘기게 된다
options api와 비슷하게 해당 lifecycle이 call되면 callback으로 선언한 function이 실행된다.
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880326#content
