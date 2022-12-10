# $router

```
action 이 후에 다른 페이지로 이동해야할 때, 이 전에는 router-link를 통해 
클릭으로 페이지를 전환했었다. 이러한 event를 code로 작성할 수 있는데,
$router 이다.
이 전에 app에 use method를 통해 router를 사용하였기에 사용 가능하다.
```

```vue
//UserList.vue

<template>
  <button @click='confirmInput'>Confirm</button> //
  <ul>
    <user-item v-for="user in users" :key="user.id" :name="user.fullName" :role="user.role"></user-item>
  </ul>
</template>

<script>
import UserItem from './UserItem.vue';

export default {
  components: {
    UserItem,
  },
  inject: ['users'],
  methods: {
    confirmInput() {
      //do something
      this.$router.push('/teams'); //
    }
  }
};
</script>
```

```
입력이 끝난 후 버튼을 클릭하면 저장이나, validate check 등 모든 행동이 끝난 후 
$router의 메소드인 push를 통해 해당 route로 이동시킨다.
$router의 method에는 여러가지가 있다. document를 참고하여 사용하면 된다.
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
