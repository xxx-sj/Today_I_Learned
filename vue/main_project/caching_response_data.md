# caching response data


```
coach 데이터를 매번 firebase에서 가져오지 않아도 될 때 caching을 통해 데이터를 저장한다.
먼저 coach store에 lastFetch라는 state를 하나 추가한다. lastFetch는 fetch를 통해 firebase에서 데이터를
가져 왔을 때 시간을 저장하기 위해서이다.
```

```js
// store/module/coaches/index.js

import mutations from './mutations'
import actions from './actions'
import getters from './getters'

export default {
  namespaced: true,
  state() {
    return {
      lastFetch: null, //
      coaches: [
          {
            id: 'c1',
            firstName: 'Maximilian',
            lastName: 'Schwarzmüller',
            areas: ['frontend', 'backend', 'career'],
            description:
              "I'm Maximilian and I've worked as a freelance web developer for years. Let me help you become a developer as well!",
            hourlyRate: 30
          },
          {
            id: 'c2',
            firstName: 'Julie',
            lastName: 'Jones',
            areas: ['frontend', 'career'],
            description:
              'I am Julie and as a senior developer in a big tech company, I can help you get your first job or progress in your current role.',
            hourlyRate: 30
          }
          ],
    }
  },
  mutations,
  actions,
  getters,
}
```

```js
// module/coaches/mutations.js

export default {
  registerCoach(state, payload) {
    state.coaches.push(payload);
  },
  setCoaches(state, payload) {
    state.coaches = payload
  }, 
  setFetchTimestamp(state) {  //
    state.lastFetch = new Date().getTime();  // timestamp 저장
  },
};
```

```js
// module/coaches/getters.js

export default {
  coaches(state) {
    return state.coaches;
  },
  hasCoaches(state) {
    return state.coaches && state.coaches.length > 0;
  },
  isCoach(_, getters, _2, rootGetters) {
    const coaches = getters.coaches;
    const userId = rootGetters.userId;
    return coaches.some(coach => coach.id === userId);
  },
  shouldUpdate(state) {
    const lastFetch = state.lastFetch;
    //처음 가져올 때 null
    if (!lastFetch) {
      return true;
    }
    const currentTimeStamp = new Date().getTime();
    //1분보다 더 지났는가? ms
    return (currentTimeStamp - lastFetch) / 1000 > 60;
  }
};
```
```
action에서 데이터를 불러올 때 mutations을 통해 FetchTime을 저장한다.
```

```js
// modules/coaches/actions.js

export default {
  async registerCoach(context, data) {
    const userId = context.rootGetters.userId;
    const coachData = {
      firstName: data.first,
      lastName: data.last,
      description: data.desc,
      hourlyRate: data.rate,
      areas: data.areas,
    };

    const response = await fetch(`https://vue-http-demo-2273e-default-rtdb.firebaseio.com/coaches/${userId}.json`, {
      method: 'PUT',
      body: JSON.stringify(coachData),
    });

    // const resonseData = await response.json();

    if(!response.ok) {
      // error ...
    }

    context.commit('registerCoach', {
      ...coachData,
      id: userId,
    });
  },

  async loadCoaches(context, payload) {
    if (!payload.forceRefresh && !context.getters.shouldUpdate) {   //
      return;                                                         //
    }

    const response = await fetch(`https://vue-http-demo-2273e-default-rtdb.firebaseio.com/coaches.json`)
    const responseData = await response.json();

    if (!response.ok) {
      const error = new Error(responseData.message || 'failed to fetch');
      throw error;
    }

    const coaches = [];

    for (const key in responseData) {
      const coach = {
        id: key,
        firstName: responseData[key].firstName,
        lastName: responseData[key].lastName,
        description: responseData[key].description,
        hourlyRate: responseData[key].hourlyRate,
        areas: responseData[key].areas,
      };

      coaches.push(coach);
    }

    context.commit('setCoaches', coaches);
    context.commit('setFetchTimestamp'); //
  }
};
```


```
fetch 조건 중 logic을 실행하기 전에 1분미만일 경우나 refresh 조건이 아닐 경우는 return 시킨다.
refresh 조건은 list page에서 refresh 버튼을 클릭할 때는 force update가 되어야 하기 때문이다.
```

```vue
// CoachesLst.vue

<template>
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
        <base-button v-if='!isCoach && !isLoading' link to='/register'>Register as Coach</base-button>
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
</template>

<script>
import CoachItem from '@/components/coaches/CoachItem';
import CoachFilter from '@/components/coaches/CoachFilter';
import BaseSpinner from '@/components/UI/BaseSpinner';
import BaseDialog from '@/components/UI/BaseDialog';

export default {
  name: 'CoachesList',
  components: { BaseDialog, BaseSpinner, CoachItem, CoachFilter },
  computed: {
    // ...mapGetters('coaches', {
    //   filteredCoaches: 'coaches',
    //   hasCoaches: 'hasCoaches'
    // })
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

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21879990#content
