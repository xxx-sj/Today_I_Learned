# vue_router_alias

```
alias는 설정한 path로 같은 component를 보여주고 싶을 때 설정한다.
```

```js
//main.js

const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', redirect: '/teams', },
    { path: '/teams', //our-domain.com/teams ...=>
    component: TeamsList,
      alias: '/', //
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

```
위와 같이 alias를 설정하면 [ localhost:8080/ ]으로 접속해도 [ localhost:8080/teams ] 와 같은 화면을 보게 된다.
단, redirect 처럼 path가 변경되지 않는다.
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
