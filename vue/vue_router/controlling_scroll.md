# controlling scroll

```
현 페이지에서 가장 아래를 보고 있는 상황에서 페이지를 전환하면 그 위치 그대로 있게되어있다.
페이지 이동 시 최상단으로 스크롤 할 수 있도록 컨트롤 하는 방법이다.
```

```
to와 from은 rotue object이다. this.$route 에 담겨있는 object로 to는 어디로 이동하는지와
from은 어디에서 이동하는지에 대한 정보이다.
savePosition은 back button을 클릭했을 때 생성되는데, 예를들어 A 페이지로 이동 했을 때는 savePosition 객체가
생성되지 않지만 A에서 back button을 클릭하게 되면 savePosition object가 생성된다 객체의 값으로는 left와 top을 갖는다.
```

```js
//main.js

const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', redirect: '/teams', },
    { path: '/teams', //our-domain.com/teams ...=>
    components: { default: TeamsList, footer: TeamsFooter },
      name: 'teams',
      alias: '/',
      children: [
        { name: 'team-members', path: ':teamId', component: TeamMembers, props: true } ,
      ],
    },
    { path: '/users', //our-domain.com/uses ...=>
      components: {default: UsersList, footer: UsersFooter}
    },
    { path: '/:notFound(.*)', component: NotFound}
  ],
  linkActiveClass: 'active',
  scrollBehavior(to, from ,savedPosition) { //
    console.log(to, from, savedPosition); //
    if (savedPosition) {                 //
      return savedPosition;             //
    }
    return { left: 0, top: 0};             //
  },
});
const app = createApp(App)

app.use(router);
app.mount('#app');
```

```
scrollBehavior method는 savePosition 처럼 [ left, top] 을 갖는 메소드를 반환해야 하고,
만약 saveePosition이 존재한다면 그 값을 반환한다. 이렇게 하면 back button을 클릭하면 이전에 보던 위치로 이동하게
되고, 다른 페이지로 이동 시 가장 상단으로 이동하게 된다.
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
