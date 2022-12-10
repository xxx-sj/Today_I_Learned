# router link style

```
router-link에 의해 생성되는 class가 있다.  [ router-link-active , router-link-exact-active ]
이 두개의 class를 갖고 css를 변경할 수 있다.
```

```css
//TheNaivgation.vue

...
a:hover,
a:active,
a.router-link-active {
  color: #f1a80a;
  border-color: #f1a80a;
  background-color: #1a037e;
}
```

```
router-link-active 와 router-link-exact-active의 차이는 
router-link-active는 nested route 에서도 적용이되지만, router-link-exact-active는 현재
활성된 item에만 적용된다.
```

```
router-link-active class name 을 router option에서 변경할 수 있다.
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
  linkActiveClass: 'active', //
});
const app = createApp(App)

app.use(router);

app.mount('#app');

```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
