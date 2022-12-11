# 라우트 매개변수 전달

```
동적 세그먼트를 통해 해당 컴포넌트로 이동해도 dummy data로 인해 같은 data list만 보여지게 된다. 
```

```js
import { createApp } from 'vue';
import { createRouter, createWebHistory } from 'vue-router';

import App from './App.vue';
import TeamsList from '@/components/teams/TeamsList';
import UsersList from '@/components/users/UsersList';
import TeamMembers from '@/components/teams/TeamMembers';

const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/teams', //our-domain.com/teams ...=>
    component: TeamsList
    },
    { path: '/users', //our-domain.com/uses ...=>
      component: UsersList
    },
    {path: '/teams/:teamId', component: TeamMembers} , //
  ],
  linkActiveClass: 'active',
});
const app = createApp(App)

app.use(router);

app.mount('#app');
```

```vue
//TeamMembers.vue

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
  </section>
</template>

<script>
import UserItem from '../users/UserItem.vue';

export default {
  components: {
    UserItem
  },
  data() {
    return {
      teamName: 'Test',
      members: [
        { id: 'u1', fullName: 'Max Schwarz', role: 'Engineer' },
        { id: 'u2', fullName: 'Max Schwarz', role: 'Engineer' },
      ],
    };
  },
};
</script>
```

```
App.vue에서 제공하는 data를 가지고 동적으로 페이지 data를 보여줄 것이다. 
```

```js
<script>
import TheNavigation from './components/nav/TheNavigation.vue';

export default {
  components: {
    TheNavigation,
  },
  data() {
    return {
      activePage: 'teams-list',
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
    };
  },
  provide() {
    return {
      teams: this.teams,
      users: this.users,
    };
  },
};
</script>
```

```
App.vue에서 provide로 제공하는 data를 App.vue에서도 router-view를 통해 TeamMembers component도 inject 할 수 있다.
TeamMembers에서 inject로 users, teams을 받아 url로 넘어오는 params을 판단하여 teams를 구별하고, teams에 존재하는
users의 list를 보여준다.
```

```
component에서 params을 판단하기 위해서는 [ params 은 routes에 path에서 [:] 으로 시작하는 값들을 포함한다.]
[ this.$route.params.[paramsName] ]을 사용한다. 이전에 paramsName으로 teamId를 사용했으므로 아래와 같이 사용하면 된다.
[ this.$route.params.teamId ]
```

```
provide로 넘어온 값들 중 params 으로 넘어온 teamId와 같은 team이 있는지 찾고, 팀이 갖고 있는 users들 중 같은 user가 있다면
배열에 넣고 마지막으로 members를 초기화하고, teamName도 초기화 한다.
```

```vue
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
    const teamId = this.$route.params.teamId; //params TeamId //t1
    const selectedTeam = this.teams.find(team => team.id === teamId); // find t1 in teams
    const members = selectedTeam.members; // members들만 빼서
    const selectedMembers = []; //마지막으로 초기화할 배열
    for(const member of members) { // for문 돌면서
      const selectedUser = this.users.find(user => user.id === member); //users의 값들중 같은 id가 있는지 확인
      selectedMembers.push(selectedUser); //push 
    }

    this.members = selectedMembers;
    this.teamName = selectedTeam.name;
  },
};
</script>
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
