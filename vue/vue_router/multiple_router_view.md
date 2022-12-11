# multiple router view

```
한개의 component내에 여러개의 router-view 태그를 사용해야할 때가 있는데, 이럴경우
main.js에서 routes를 정의할 때 component로 한개를 정의하는 것이 아닌 components로 여러개의 
component를 정의하면 된다. components의 value로는 object가 사용되며, [key : value]로 정의한다.
여기서 key는 router-link에 설정한 name이 들어가게 된다. slot가 마찬가지로 router-link에  name을 
정의하지 않으면 default 값으로 설정한다.
```

```js
//main.js

const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', redirect: '/teams', },
    { path: '/teams', //our-domain.com/teams ...=>
    components: { default: TeamsList, footer: TeamsFooter }, //
      name: 'teams',
      alias: '/',
      children: [
        { name: 'team-members', path: ':teamId', component: TeamMembers, props: true } , 
      ],
    },
    { path: '/users', //our-domain.com/uses ...=>
      components: {default: UsersList, footer: UsersFooter} //
    },
    { path: '/:notFound(.*)', component: NotFound}
  ],
  linkActiveClass: 'active',
});
const app = createApp(App)

app.use(router);

app.mount('#app');
```

```vue
//App.vue

<template>
  <the-navigation></the-navigation>
  <main>
    <router-view></router-view> //
  </main>
  <footer>
    <router-view name='footer'></router-view> //
  </footer>
</template>

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

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
