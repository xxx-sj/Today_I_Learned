# vue_router_redirect

```
vue-router에도 어떠한 path에 대해 redirect를 시킬 수 있다.
처음 기볺화면 [localhost:8080/] 에서 TeamsList component를 보여주고 싶다면 아래와 같이 사용한다. 
```

```js
//main.js

import { createApp } from 'vue';
import { createRouter, createWebHistory } from 'vue-router';

import App from './App.vue';
import TeamsList from '@/components/teams/TeamsList';
import UsersList from '@/components/users/UsersList';
import TeamMembers from '@/components/teams/TeamMembers';

const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', redirect: '/teams', }, //
    { path: '/teams', //our-domain.com/teams ...=>
    component: TeamsList
    },
    { path: '/users', //our-domain.com/uses ...=>
      component: UsersList
    },
    { path: '/teams/:teamId', component: TeamMembers, props: true } ,
  ],
  linkActiveClass: 'active',
});
const app = createApp(App)

app.use(router);

app.mount('#app');
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
