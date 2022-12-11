# 동적 세그먼트

```
vue-router path에 /teams/t1 , /teams/t2 와 같이 개별적인 팀에 대한 정보를 보고싶은 페이지가 있다면 아래와 같이 사용하면 된다

[ { path: 'teams/:teamId' } ]

```

```vue
//main.js

const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/teams', //our-domain.com/teams ...=>
    component: TeamsList
    },
    { path: '/users', //our-domain.com/uses ...=>
      component: UsersList
    },
    {path: '/teams/:teamId'},
    {path: '/teams/new'}
  ],
  linkActiveClass: 'active',
});
const app = createApp(App)

app.use(router);

app.mount('#app');
```

```
여기서 routes의 순서에 문제가 있는데, :teamId를 통해 동적 세그먼트를 설정하게 되면 teamId로써 무엇이든 오면
해당 페이지로 이동하게 되어버린다. 다시말해  "teams/t1", "teams/t2" 와 "/teams/new", "teams/old" 도 동일하게
인식하게 되어버린다. 이 문제를 해결하기 위해서는 "teams/t2" 와 "/teams/new" 는 동적 세그먼트 path 위에 선언하면 된다.
```

```js
const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/teams', //our-domain.com/teams ...=>
    component: TeamsList
    },
    { path: '/users', //our-domain.com/uses ...=>
      component: UsersList
    },
    {path: '/teams/new'},
    {path: '/teams/:teamId'}
  ],
  linkActiveClass: 'active',
});
const app = createApp(App)

app.use(router);
app.mount('#app');
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
