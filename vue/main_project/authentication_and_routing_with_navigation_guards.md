# authentication and routing with navigation guareds

```
로그인이나 로그아웃을 한 후 특정 페이지로 redirect 시키기 위해 아래와 같이 한다.
먼저, logout을 했을 때는 auth 페이지로 이동하게 만든다.
```

```vue
//TheHeader.vue

<template>
  <header>
    <nav>
      <h1><router-link to='/'>Find a Coach</router-link></h1>
      <ul>
        <li><router-link to='/coaches'>All Coaches</router-link></li>
        <li v-if='isLoggedIn'><router-link to='/requests'>Requests</router-link></li>
        <li v-else>
          <router-link to='/auth'>Login</router-link>
        </li>
        <li v-if='isLoggedIn'>
          <base-button @click='logout'>Logout</base-button>
        </li>
      </ul>
    </nav>
  </header>
</template>

<script>
export default {
  name: 'TheHeader',
  computed: {
    isLoggedIn() {
      return this.$store.getters['auth/isAuthenticated'];
    }
  },
  methods: {
    logout() {
      this.$store.dispatch('auth/logout');
      this.$router.replace('/coaches');                   //
    }
  }
};
</script>

<style scoped>
header {
  width: 100%;
  height: 5rem;
  background-color: #3d008d;
  display: flex;
  justify-content: center;
  align-items: center;
}

header a {
  text-decoration: none;
  color: #f391e3;
  display: inline-block;
  padding: 0.75rem 1.5rem;
  border: 1px solid transparent;
}

a:active,
a:hover,
a.router-link-active {
  border: 1px solid #f391e3;
}

h1 {
  margin: 0;
}

h1 a {
  color: white;
  margin: 0;
}

h1 a:hover,
h1 a:active,
h1 a.router-link-active {
  border-color: transparent;
}

header nav {
  width: 90%;
  margin: auto;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

header ul {
  list-style: none;
  margin: 0;
  padding: 0;
  display: flex;
  justify-content: center;
  align-items: center;
}

li {
  margin: 0 0.5rem;
}
</style>
```

```
다음 로그인일 때, 로그인 할 때는 UserAuth component에서 로그인을 진행하는데, 로그인이 된다면 
coach list 페이지로 바로 redirect 시킬 수 있지만, coach list 페이지에서 login button을 클릭하여 진행 시에
register 페이지로 redirect시키고 싶다면, 아래와 같이 작성하면 된다.
```

```vue
//UserList.vue

<template>
  <div>
    <base-dialog :show='!!error' title='An error occurred!' @close='handleError'>
      <p>{{ error }}</p>
    </base-dialog>
    <section>
      <coach-filter @change-filter='setFilter'></coach-filter>
    </section>
    <section>
      <base-card>
        <div class='controls'>
          <base-button mode='outline' @click='loadCoaches(true)'>Refresh</base-button>
          <base-button link to='/auth?redirect=register' v-if='!isLoggedIn'>Login to Register as Coach</base-button>            //
          <base-button v-if='isLoggedIn && !isCoach && !isLoading' link to='/register'>Register as Coach</base-button>
        </div>
        <div v-if='isLoading'>
          <base-spinner />
        </div>
        <ul v-else-if='hasCoaches'>
          <coach-item v-for='coach in filteredCoaches' :key='coach.id'
                      :id='coach.id'
                      :first-name='coach.firstName'
                      :last-name='coach.lastName'
                      :rate='coach.hourlyRate'
                      :areas='coach.areas'
          />
        </ul>
        <h3 v-else>No caches Found.</h3>
      </base-card>
    </section>
  </div>
</template>

<script>
import CoachItem from '@/components/coaches/CoachItem';
import CoachFilter from '@/components/coaches/CoachFilter';
import BaseSpinner from '@/components/UI/BaseSpinner';
import BaseDialog from '@/components/UI/BaseDialog';
import BaseButton from '@/components/UI/BaseButton';

export default {
  name: 'CoachesList',
  components: { BaseButton, BaseDialog, BaseSpinner, CoachItem, CoachFilter },
  computed: {
    // ...mapGetters('coaches', {
    //   filteredCoaches: 'coaches',
    //   hasCoaches: 'hasCoaches'
    // })
    isLoggedIn() {
      return this.$store.getters['auth/isAuthenticated'];
    },
    filteredCoaches() {
      const coaches = this.$store.getters['coaches/coaches']
      return coaches.filter( coach => {
        if (this.activeFilters.frontend && coach.areas.includes('frontend')) {
          return true;
        }
        if (this.activeFilters.backend && coach.areas.includes('backend')) {
          return true;
        }
        if (this.activeFilters.career && coach.areas.includes('career')) {
          return true;
        }
        return false;
      })
    },
    hasCoaches() {
      return !this.isLoading && this.$store.getters['coaches/hasCoaches']
    },
    isCoach() {
      return this.$store.getters['coaches/isCoach']
    }
  },
  data() {
    return {
      isLoading: false,
      error: null,
      activeFilters: {
        frontend: true,
        backend: true,
        career: true,
      }
    }
  },
  created() {
    this.loadCoaches(false);
  },
  methods: {
    setFilter(updatedFilters) {
      this.activeFilters = updatedFilters;
    },

    async loadCoaches(refresh = false) {
      this.isLoading = true;
      try {
        await this.$store.dispatch('coaches/loadCoaches', {forceRefresh: refresh});
      } catch(error) {
        this.error = error.message || 'Something went wrong!';
      }
      this.isLoading = false;

    },
    handleError() {
      this.error = null;
    }
  },
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}

.controls {
  display: flex;
  justify-content: space-between;
}
</style>
```

```
router-link 에 query를 추가하여 [ /auth/redirect=register ] login 페이지에서 동적으로 redirect할 페이지를 전달받게 한다.
login 페이지에서는 아래와 같이 작성한다.
```

```vue
//UserAuth.vue

<template>
  <div>
    <base-dialog :show='!!error' title='An error occurred' @close='handleError'>
      <p>{{ error }}</p>
    </base-dialog>
    <base-dialog :show='isLoading' title='Authenticating...' fixed>
      <p>Authenticating...</p>
      <base-spinner></base-spinner>
    </base-dialog>
    <base-card>
        <form @submit.prevent='submitForm'>
          <div class='form-control'>
            <label for='email'>E-Mail</label>
            <input type='email' id='email' v-model.trim='email'>
          </div>
          <div class='form-control'>
            <label for='password'>Password</label>
            <input type='password' id='password' v-model.trim='password'>
          </div>
          <p v-if='!formIsValid'>Please enter a valid email and password (must be at least 6 characters long).</p>
          <base-button>{{ submitButtonCaption }}</base-button>
          <base-button type='button' mode='flat' @click='switchAuthMode'>{{ switchModeButtonCaption }}</base-button>
        </form>
    </base-card>
  </div>
</template>

<script>
import BaseButton from '@/components/UI/BaseButton';
import BaseSpinner from '@/components/UI/BaseSpinner';
import BaseDialog from '@/components/UI/BaseDialog';
export default {
  name: 'UserAuth',
  components: { BaseDialog, BaseSpinner, BaseButton },
  data() {
    return {
      email: '',
      password: '',
      formIsValid: true,
      mode: 'login',
      isLoading: false,
      error: null,
    };
  },
  computed: {
    submitButtonCaption() {
      if (this.mode === 'login') {
        return 'Login';
      } else {
        return 'Signup';
      }
    },
    switchModeButtonCaption() {
      if (this.mode === 'login') {
        return 'Signup instead';
      } else {
        return 'Login instead';
      }
    },
  },
  methods: {
    async submitForm() {
      this.formIsValid = true;
      if (this.email === '' || !this.email.includes('@') || this.password.length < 6) {
        this.formIsValid = false;
        return;
      }

      this.isLoading = true;

      const actionPayload = {
        email: this.email,
        password: this.password,
      }

      try {
        if (this.mode === 'login') {
          await this.$store.dispatch('auth/login', actionPayload)
        } else {
          await this.$store.dispatch('auth/signup', actionPayload);
        }
        const redirectUrl = `/${this.$route.query.redirect || 'coaches'}`;          //
        this.$router.replace(redirectUrl);                                           //
      } catch (err) {
        this.error = err.message || 'Failed to authenticate, try later.';
      }

      this.isLoading = false;
    },
    switchAuthMode() {
      if (this.mode === 'login') {
        this.mode = 'signup';
      } else {
        this.mode = 'login'
      }
    },

    handleError() {
      this.error = null;
    }
  }
};
</script>

<style scoped>
form {
  margin: 1rem;
  padding: 1rem;
}

.form-control {
  margin: 0.5rem 0;
}

label {
  font-weight: bold;
  margin-bottom: 0.5rem;
  display: block;
}

input,
textarea {
  display: block;
  width: 100%;
  font: inherit;
  border: 1px solid #ccc;
  padding: 0.15rem;
}

input:focus,
textarea:focus {
  border-color: #3d008d;
  background-color: #faf6ff;
  outline: none;
}
</style>
```

```
query에서 해당 redirect 값을 꺼내어 있다면 전달받은 페이지로, 없다면 coach list 페이지로 redirect 시킨다.
```

### global navigation guards

```
다음으로는 페이지마다 navigation-guards 를 사용하여 특정 페이지는 로그인이 필요하게, 또 다른 페이지는 로그인이 없어도
접근 가능하게 하기 위해 navigation-guards 를 작성한다.
```

```js
//router.js


import { createRouter, createWebHistory } from 'vue-router'

import CoachDetail from '@/pages/coaches/CoachDetail';
import CoachesList from '@/pages/coaches/CoachesList';
import CoachRegistration from '@/pages/coaches/CoachRegistration';

import ContactCoach from '@/pages/requests/ContactCoach';
import RequestsReceived from '@/pages/requests/RequestsReceived';
import NotFound from '@/pages/NotFound';
import UserAuth from '@/pages/auth/UserAuth';
import store from './store/index'

const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', redirect: '/coaches' },
    { path: '/coaches', component: CoachesList },
    { path: '/coaches/:id', props:true, component: CoachDetail, children: [
        { path: 'contact', component: ContactCoach }, // /coaches/c1/contact
      ] },
    { path: '/register', component: CoachRegistration, meta: { requiresAuth: true} },               //
    { path: '/requests', component: RequestsReceived,  meta: { requiresAuth: true} },               //
    { path: '/auth', component: UserAuth,  meta: { requiresUnauth: true} },                          //
    { path: '/:notFound(.*)', component: NotFound },

  ],
});

router.beforeEach((to, _, next) => {                                                                //
  if (to.meta.requiresAuth && !store.getters['auth/isAuthenticated']) {                             //
    next('/auth');                                                                                 //
  } else if (to.meta.requiresUnauth && store.getters['auth/isAuthenticated']) {                    //
    next('/coaches');                                                                           //
  } else {                                                                                        //
    next();                                                                                    //
  }
});

export default router;
```

```
먼저 현재 로그인 상태인지 아닌지를 판별하기 위해 root store인 store/index를 import 한다. 
여기서는 이전 store를 사용하던것과 같이 사용 가능하다. 대신 component내에서 사용하던 this.$store는 아니다.
routes 중 meta값으로 해당 페이지가 로그인이 필요한 페이지인지, 아니면 필요없는 페이지에 대해 값을 설정한다.

router의 beforeEach를 통해 먼저, 
[ 향하는 페이지[to]가 로그인이 필요하고 && 로그인이 되어있지 않는 상태라면 ] 로그인페이지로,
[ 향하는 페이지가 로그인이 필요없고 && 로그인한 상태라면 ] coach list 페이지로
그도 아니라면 나머지는 모두 pass 시킨다. 
next인자로는 boolean값을 전달 할 수 있는데, 기본적으로는 true이며 false 전달 시 router가 동작하지 않는다.
```

```
하나 주의해야할 점은 현재 login 상태값을 vuex/store 에만 저장하고 있기 때문에 auth 페이지 같은경우 로그인 없이도 접근이 
가능하나, url을 통해 테스트 진행시 http://localhost:8080/auth를 작성하고 엔터를 치면 vue app이 restart되면서 결국에 
vue/store가 초기화되면서 데이터가 지워진다. 따라서 login이 풀리게 된다. [router-link로 이동시에는 풀리지 않음 ]
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880050#content
