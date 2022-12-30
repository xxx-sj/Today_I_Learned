# add vuex and data

![image](https://user-images.githubusercontent.com/62305110/210093360-f5956317-0503-4935-9876-9b2667b58326.png)

```
vuex를 통해 coaches 데이터와 requests 데이터를 추가한다.
먼저 main store에 vuex store를 만드는 코드를 입력한다.
```

```js
// storeindex.js

import { createStore } from 'vuex';

import coachesModule from './modules/coaches/index'

const store = createStore({
  modules: {
    coaches: coachesModule,
  },
});

export default store; // 이후 main.js에서 import 받을 수 있게
```



```js
// store/modules/coaches/index.js

import mutations from './mutations'
import actions from './actions'
import getters from './getters'

export default {
  namespaced: true,
  state() {
    return {
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
// store/modules/coaches/getters.js

export default {
  coaches(state) {
    return state.coaches;
  },
  hasCoaches(state) {
    return state.coaches && state.coaches.length > 0;
  }
};
```

```
getters component에서 사용하기
```

```vue
// CoachesList.vue

<template>
  <section>
    FILTER
  </section>
  <section>
    <div class='controls'>
      <button>Refresh</button>
      <router-link to='/register'>Register as Coach</router-link>
    </div>
    <ul v-if='hasCoaches'>
      <li v-for='coach in filteredCoaches' :key='coach.id'>
        {{ coach.firstName }}
      </li>
    </ul>
    <h3 v-else>No caches Found.</h3>
  </section>
</template>

<script>
import { mapGetters } from 'vuex'

export default {
  name: 'CoachesList',
  computed: {
    ...mapGetters('coaches', {
      filteredCoaches: 'coaches',
      hasCoaches: 'hasCoaches'
    })
  }
};
</script>
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
