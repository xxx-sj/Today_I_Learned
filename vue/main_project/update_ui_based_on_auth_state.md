# update ui, based on auth state

```
auth state에 따라 request coach를 등록하는 것과 requests를 확인하는 부분을 login 버튼으로 보이게 ui를 변경한다.
```

```
먼저 auth/getter로 이동하여 현재 상태가 login 되었는지에 대한 상태를 확인하기 위해 state에 token값이 있다면 
login 한 것으로 값을 확인하는 getter를 만든다.
```

```js
//auth/getters.js

export default {
  userId(state) {
    return state.userId;
  },
  token(state) {
    return state.token;
  },
  isAuthenticated(state) {
    return !!state.token;
  }
}
```

```
이제 login 값을 사용하는 UI component로 이동하여 값을 적용한다.
```

```vue
//CoachList.vue

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
          <base-button link to='/auth' v-if='!isLoggedIn'>Login</base-button>                   //
          <base-button v-if='isLoggedIn && !isCoach && !isLoading' link to='/register'>Register as Coach</base-button>      //
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
    isLoggedIn() {                                                        //
      return this.$store.getters['auth/isAuthenticated'];                 //
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
먼저 coach list를 보여주는 component이다. 로그인을 하지않으면 coach 등록 부분에 login button을, 로그인 하였다면
coach 등록버튼을 보여주게 한다.
```

```vue
//TheHeader.vue

<template>
  <header>
    <nav>
      <h1><router-link to='/'>Find a Coach</router-link></h1>
      <ul>
        <li><router-link to='/coaches'>All Coaches</router-link></li>                 //
        <li v-if='isLoggedIn'><router-link to='/requests'>Requests</router-link></li> //
        <li v-else>
          <router-link to='/auth'>Login</router-link>
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
      return this.$store.getters['auth/isAuthenticated'];           //
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
header 부분도 마찬가지로 로그인이 되어있지않다면 login router-link를 아니라면 requests 로 이동하는 router-link를 보여준다.
```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880038#content
