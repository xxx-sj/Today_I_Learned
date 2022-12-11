# nested route

```
/teams 는 list를 나타내는 component를 보여주게 되고, /teams/t1 은 team list 중 t1에 해당하는 
팀의 상세정보를 확인할 수 있는 페이지를 보여주게 된다. 이러한 경우 /team path 아래 children이라는 속성을
추가하여 설정할 수 있다. 
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
      children: [
        { path: ':teamId', component: TeamMembers, props: true } , // teams/:teamId
      ],
    },
    { path: '/users', //our-domain.com/uses ...=>
      component: UsersList
    },
    { path: '/:notFound(.*)', component: NotFound},
    //{ path: '/teams/:teamId', component: TeamMembers, props: true } ,
  ],
  linkActiveClass: 'active',
});
const app = createApp(App)

app.use(router);

app.mount('#app');
```

```
이 전에는 같은 level에 path가 /teams/:teamId 와 /teams이 같이 있었지만 앞에 path가 같아, children에 넣으면서
path 가 [ /teams/:teamId ] -> [ :teamId ] 로변경되었다. 이유는 /teams 뒤에 붙을 path만 적어주면 되기 때문이다.
children의 value로는 route와 같은 배열이 들어가게 된다.
```

```
children으로 route를 작성 시 문제가 있는데, App.vue에 작성한 router-view tag는 root routes 즉, main.js에 작성한 [children 제외]
top property에 대해서만 렌더링을하기 때문이다. children에 작성한 route는 어디에 렌더링을 해야하는지 router는 알지 못한다.
이 문제는 children route의 parent router인 component에 [ router-view ] 태그를 작성하면 해결 할 수 있다.
children route는 이제 parent component의 router-view에 렌더링된다.
```

```vue

<template>
  <router-view></router-view> //
  <ul>
    <teams-item
      v-for="team in teams"
      :key="team.id"
      :id='team.id'
      :name="team.name"
      :member-count="team.members.length"
    ></teams-item>
  </ul>
</template>
```

```
이 전에 root route에 작성할 때는 router-link에 작성한 /temas 와 /users는 nav에서 css가 강조가 되었지만
/teams/:teamId path는 앞이 /teams임에도 강조가 되지 않았다. children으로 해당 path가 들어감에 따라 
강조 css가 동작을 했지만, router-link-exact-active는 class로 들어가지 않는다. 
router-link-exact-active는 해당 path /teams 에서만 동작한다.
```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
