# some router link

```
라우터로 이동한 페이지에서 같은 라우터 주소를 가진 페이지로 이동할 때가 있다.
[ teams/t1 ] -> [ teams/t2 ] t1에서 t2로 이동.

같은 component로 이동하게 되면 url은 변경되지만 data는 업데이트 되지않는다.
같은 component일 때 vue-router는 component를 파괴하지않고 loaded된 component를 다시 빌드합니다.
```

```vue
//TemMember.vue

<template>
  <section>
    <h2>{{ teamName }}</h2>
    <ul>
      <user-item
        v-for="member in members"
        :key="member.id"
        :name="member.fullName"
        :role="member.role"
      ></user-item>
    </ul>
    <router-link to='/teams/t3'>Go to Team2</router-link>
  </section>
</template>

<script>
import UserItem from '../users/UserItem.vue';

export default {
  inject: ['users', 'teams'],
  components: {
    UserItem
  },
  data() {
    return {
      teamName: '',
      members: [],
    };
  },
  created() {
    //this.$route.path // teams/t1
    /*
     teams: [
        { id: 't1', name: 'Frontend Engineers', members: ['u1', 'u2'] },
        { id: 't2', name: 'Backend Engineers', members: ['u1', 'u2', 'u3'] },
        { id: 't3', name: 'Client Consulting', members: ['u4', 'u5'] },
      ],
      users: [
        { id: 'u1', fullName: 'Max Schwarz', role: 'Engineer' },
        { id: 'u2', fullName: 'Praveen Kumar', role: 'Engineer' },
        { id: 'u3', fullName: 'Julie Jones', role: 'Engineer' },
        { id: 'u4', fullName: 'Alex Blackfield', role: 'Consultant' },
        { id: 'u5', fullName: 'Marie Smith', role: 'Consultant' },
      ],
    */
    console.log(this.$route)
    const teamId = this.$route.params.teamId;
    const selectedTeam = this.teams.find(team => team.id === teamId);
    const members = selectedTeam.members;
    const selectedMembers = [];
    for(const member of members) {
      const selectedUser = this.users.find(user => user.id === member);
      selectedMembers.push(selectedUser);
    }

    this.members = selectedMembers;
    this.teamName = selectedTeam.name;
  },
};
</script>
```

```
위와같이 created에서 작성 시 vue-router는 component를 파괴하지도, recreate 하지 않기 때문에 
데이터의 변경이 없다.
```

```
변경하기 위해서는 어떻게 해야하는가? 
this.$route 속성을 이용해서 변경한다. this.$route은 최신 정보를 보유하고 있으며 url이 변경되면 업데이트 된다.
watch 속성에 $route를 통해 감시하면서 데이터가 update되면 method를 실행하게 만든다.
```

```vue

<router-link to='/teams/t3'>Go to Team2</router-link>

<script>
import UserItem from '../users/UserItem.vue';

export default {
  inject: ['users', 'teams'],
  components: {
    UserItem
  },
  data() {
    return {
      teamName: '',
      members: [],
    };
  },
  methods: {
    loadTeamMembers(route) { //
      const teamId = route.params.teamId;
      const selectedTeam = this.teams.find(team => team.id === teamId);
      const members = selectedTeam.members;
      const selectedMembers = [];
      for(const member of members) {
        const selectedUser = this.users.find(user => user.id === member);
        selectedMembers.push(selectedUser);
      }

      this.members = selectedMembers;
      this.teamName = selectedTeam.name;
    }
  },
  created() {
    this.loadTeamMembers(this.$route); //
  },
  watch: {
    $route(newRoute) {
      this.loadTeamMembers(newRoute); //
    },
  },
};
</script>
```

```
route를 메소드의 매개변수로 넘기는 이유는 route의 params를 판별하기 위해서 이다.
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
