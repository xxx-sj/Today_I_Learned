# vue_router_name_alias

```
router-link의 to에 전달하는 value 값을 routes에 정의한 값으로 설정할 수 있다.
routes의 name option을 통해 routes의 alias를 설정하고, 만약에 route에 dynamic segment가 있다면,
params을 통해 전달할 수 있다.
params는 object로 key , value로 값을 전달할 수 있다.
이전에는 path를 다 썼다면 name을 통해 path를 정할 수 있다. path 수정이 들어가게 되면 앱이 커짐에 따라
수정해야할 부분이 많아지겠지만 name을 사용하면 그럴일이 줄어든다.
```

```vue
//TeamsItem.vue

<template>
  <li>
    <h3>{{ name }}</h3>
    <div class="team-members">{{ memberCount }} Members</div>
    <router-link :to='teamMemberLink'>View Members</router-link>
  </li>
</template>

<script>
export default {
  props: ['id', 'name', 'memberCount'],
  computed: {
    teamMemberLink() {
      // return `/teams/${this.id}`;
      return { name: 'team-members', params: {'teamId': this.id} };
    }
  }
};
</script>
```

```
code로 router를 이동시킬 때도 string 대신 object를 써서 이동할 수 있다.

this.$router.push( { name: 'team-members', params: {'teamId': this.id} } )
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
