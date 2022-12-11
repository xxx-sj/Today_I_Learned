# vue-router routes options

```js
//main.js

import { createApp } from 'vue';
import { createRouter, createWebHistory } from 'vue-router';

import App from './App.vue';

const router = createRouter({
  history: createWebHistory(),
  routes: [],
});
const app = createApp(App)

app.mount('#app');
```

```
routes options으로 object를 전달하게 된다.
object의 property로는 아래와 같다.

- path : 도메인 이후의 path // 라우터에게 알려주는 path이다.
- component: path에 해당하는 component이다. path로 이동 시 보여줄 component를 전달한다.
```

```js
//main.js

import { createApp } from 'vue';
import { createRouter, createWebHistory } from 'vue-router';

import App from './App.vue';
import TeamsList from '@/components/teams/TeamsList';
import UsersList from '@/components/users/UsersList';

const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/teams', //our-domain.com/teams ...=>
    component: TeamsList
    },
    { path: '/users', //our-domain.com/uses ...=>
      component: UsersList
    },
  ],
});
const app = createApp(App)

app.mount('#app');
```

```
이제 vue instance가 이 router를 인식할 수 있도록 builtin method 인 use를 통해 인자로 전달한다.
use method는 third party package를 사용할 수 있도록 하는 method 이다.
```

```js
//main.js

import { createApp } from 'vue';
import { createRouter, createWebHistory } from 'vue-router';

import App from './App.vue';
import TeamsList from '@/components/teams/TeamsList';
import UsersList from '@/components/users/UsersList';

const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/teams', //our-domain.com/teams ...=>
    component: TeamsList
    },
    { path: '/users', //our-domain.com/uses ...=>
      component: UsersList
    },
  ],
});
const app = createApp(App)

app.use(router);

app.mount('#app');
```

```
router를 등록은 했지만 router가 어디에 component를 load해야 하는지는 모르는 상황이다.
router가 렌더링할 위치를 알려주기 위해 특별한 element를 사용한다. [ <router-view></router-view> ]
이렇게 사용하게 되면 router는 해당 element에 component를 load하게 되고, 이전과는 달리 해당 component 들을
local이나 global 에 import 할 필요가 없어진다.
```

```vue
//App.vue

<template>
  <the-navigation @set-page="setActivePage"></the-navigation>
  <main>
    <router-view></router-view>
  </main>
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
  methods: {
    setActivePage(page) {
      this.activePage = page;
    },
  },
};
</script>
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
