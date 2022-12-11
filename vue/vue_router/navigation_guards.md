# navigation guards


**Global_beforeEach**
```
main.js에서 vue app에 router를 전달하기 전 router guards를 설정할 수 있다.
beforeEach에 전달한 callback method는 vue-router에 의해 호출되는데, 페이지를 이동할 때마다 호출된다. 
[ 페이지 이동 전에 before ]
callback method에는 3개의 인수를 받는데,  [ to, from, next ] 이다.
to는 어디로 이동할지에 대한 page 정보이고, from은 어디에서 이동을 하는지에 대한 정보, 마지막으로 next는
이동을 할지 말지에 대한 메소드이다.
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
  next(false); //
});

const app = createApp(App)
app.use(router);
app.mount('#app');
```


```
next method에 false를 인자로 넘기면 navigation false가 되어 페이지가 렌더링되지 않는다.
next 인자로 path string을 전달하면 해당 path로 이동하고 또는 navigation object 를 전달하면 
navigation object에 담긴 property정보를 갖고 해당하는 path로 이동한다.
```

![image](https://user-images.githubusercontent.com/62305110/206898023-aad24a7a-d624-4e58-aa76-1204a6ee0b6e.png)

```js
router.beforeEach(function(to, from, next) {
  console.log('Global beforeEach')
  console.log(to, from);
  if (to.name === 'team-members') { // 이동하려는 object의 name이 같으면 루프를 돌기 때문에 team-member라면 통과 시킨다.
    next();
  } else {
    next({name: 'team-members', params: {teamId: 't2'}});
  }
});
```

**beforeEnter [ local ]**
```
global이 아닌 개별적 route에도 설정할 수 있다.
```

```js
{ path: '/users', //our-domain.com/uses ...=>
    components: {default: UsersList, footer: UsersFooter},
    beforeEnter(to, from, next) {
      console.log('users beforeEnter');
      console.log(to, from);
      next();
    },
  },
```

```
global과 마찬가지로 to , from을 갖고 next 도 같은 메소드 이다.
```

**beforeRouteEnter [ hook ]**
```
각 component에 사용할 수 있는 hook method 이다.
beforeRouteEnter method는 내비게이션 전에 호출되는 hook 이다.
```
```js
<script>
import UserItem from './UserItem.vue';

export default {
  components: {
    UserItem,
  },
  inject: ['users'],
  methods: {
    confirmInput() {
      //do something
      this.$router.push('/teams');
    }
  },
  beforeRouteEnter(to, from, next) {
    console.log("userList  cmp beforeRouteEnter");
    console.log(to, from);
    next();
  }
};
</script>
```

**beforeRouteUpdate [ local ]**
```
이 전에 watch로 prop으로 넘기는 teamId 변경에 따라 cmp를 변경해왔지만 
beforeRouteUpdate hook method로 route변경을 감지하여 update할 수도 있다.
```

```js
//TeamMember.vue

 beforeRouteUpdate(to, from, next) {
    this.loadTeamMembers(to.params.teamId);
    console.log(to, from);
    next();
  },
```

![image](https://user-images.githubusercontent.com/62305110/206900922-10431bd1-2337-4bd7-89dd-6fb6e0fa76fa.png)


**afterEach [global] **

```
beforeEach와 같이 global하게 등록할 수 있으며 main.js에서 정의한다.
afterEach의 callback method로 [ to, from ] 만 갖는다. 이유는 beforeEach 처럼
네비게이션 전에 호출되는 것이 아닌, 이후에 호출되는 것이기 때문에 next가 없다.
이 메소드에서는 분석 데이터를 쌓는데 유용하다. 서버에 navigation action 이나, user가 page를 change 하는 일들을
로그로 쌓는다.
```

```js
//main.js

router.afterEach(function(to, from) {
  console.log(to, from);
  //sending analytics data;
});
```

**beforeRouteLeave [local] **
```
form으로 입력할 수 있는 페이지가 있다면, 실수로 다른 페이지 이동을 누를 경우 
다시 돌아왔을 때 데이터가 날아가 버린다. 이런 상황을 방지하고자 cmp에 beforeRouteLeave hook method를 제공하는데
다른 router-guard hook과 마찬가지로 [ to, from, next ] 를 인자로 받는다.
만약 페이지를 떠나기 전 확인해야 할 사항이 있을경우 사용하면 유용하다.
unmounted life hook 은 global gaurd가 호출된 이후에 호출되기 때문에 막지 못한다.
```

```vue
//UserList.vue

<template>
  <button @click='confirmInput'>Confirm</button>
  <button @click='saveChanges'>Save changes</button>
  <ul>
    <user-item v-for="user in users" :key="user.id" :name="user.fullName" :role="user.role"></user-item>
  </ul>
</template>

<script>
import UserItem from './UserItem.vue';

export default {
  components: {
    UserItem,
  },
  inject: ['users'],
  data() {
    return {
      changesSaved: false,
    }
  },
  methods: {
    confirmInput() {
      //do something
      this.$router.push('/teams');
    },
    saveChanges() {
      this.changesSaved = true;
    }
  },
  beforeRouteEnter(to, from, next) {
    console.log("userList  cmp beforeRouteEnter");
    console.log(to, from);
    next();
  },
  beforeRouteLeave(to, from, next) {                    //
    console.log('userList cmp beforeRouteLeave');       / /
    console.log(to, from);                             //
    if (this.changesSaved) {                           //
      next();
    } else {
      let userWantsToLeave = confirm('Are tou sure? You got unsaved changes!');
      next(userWantsToLeave);
    }                                                    //

  },
  unmounted() {
    console.log('unmounted');
  },
};
</script>
```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
