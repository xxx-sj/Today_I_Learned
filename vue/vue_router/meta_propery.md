# meta property

```
routes를 정의할 때 extraData 처럼 meta라는 property가 존재해 meta의 value로 
object를 정의해 필요한 데이터를 정의할 수 있다. 
이러한 방식으로 route 이동 시 페이지에 Auth 인증이 필요한지 아닌지를 구별할수도 있다.
```

```js
const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', redirect: '/teams', },
    { path: '/teams', //our-domain.com/teams ...=>
    components: { default: TeamsList, footer: TeamsFooter },
      meta: { needsAuth: true,},   //
      name: 'teams',
      alias: '/',
      children: [
        { name: 'team-members', path: ':teamId', component: TeamMembers, props: true } ,
      ],
    },
    { path: '/users', //our-domain.com/uses ...=>
      components: {default: UsersList, footer: UsersFooter},
      beforeEnter(to, from, next) {
        console.log('users beforeEnter');
        console.log(to, from);
        next();
      },
    },
    { path: '/:notFound(.*)', component: NotFound}
  ],
  linkActiveClass: 'active',
  scrollBehavior(_, _2 ,savedPosition) {
    // console.log(to, from, savedPosition);
    if (savedPosition) {
      return savedPosition;
    }
    return { left: 0, top: 0};
  },
});

router.beforeEach(function(to, from, next) {
  console.log('Global beforeEach')
  console.log(to, from);
  if (to.meta.needsAuth) {
    console.log('Needs auth!');   //
    next();                       //
  }
  // if (to.name === 'team-members') {
  //   next();
  // } else {
  //   next({name: 'team-members', params: {teamId: 't2'}});
  // }
  next();
});

router.afterEach(function(to, from) {
  console.log(to, from);
  //sending analytics data;
});

const app = createApp(App)
app.use(router);
app.mount('#app');

```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
