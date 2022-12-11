# not found page

```
사용자가 아무 url을 입력해서 들어올 경우를 대비해 not found page를 작성할 수 있다.
routes의 최하위에 작성해야 하며, 작성하지 않는다면 다른 route들을 덮어쓰게 되어버린다. 
```

```js
//main.js

const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', redirect: '/teams', },
    { path: '/teams', //our-domain.com/teams ...=>
    component: TeamsList,
      alias: '/',
    },
    { path: '/users', //our-domain.com/uses ...=>
      component: UsersList
    },
    { path: '/teams/:teamId', component: TeamMembers, props: true } ,
     { path: '/:notFound(.*)', component: NotFound}
  ],
  linkActiveClass: 'active',
});
const app = createApp(App)

app.use(router);

app.mount('#app');
```

```
작성은 [ { path: '/:[name](.*)' }] 이며 name은 짓고 싶은대로 작성 할 수 있다.
(.*)는 vue-router에서 제공하는 정규 표현식이다.
[ localhost:8080/somthing ] 와 같이 path에 정의되지않은 것으로 요청 시 NotFound component로 이동하게 된다.
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
