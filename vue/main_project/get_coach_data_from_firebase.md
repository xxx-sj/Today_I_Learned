# get coach data from firebase

```
fetch get을 통해 firebase에서 list를 가져오기
state를 바로 변경하는 것은 권장하지않기 때문에 mutations와 actions을 작성한다.
```

```js
// store/modules/coach/mutations.js

export default {
  registerCoach(state, payload) {
    state.coaches.push(payload);
  },
  setCoaches(state, payload) {
    state.coaches = payload
  }
};
```

```js
// store/modules/coaches/actions.js

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

  async loadCoaches(context) {
    const response = await fetch(`https://vue-http-demo-2273e-default-rtdb.firebaseio.com/coaches.json`)
    const responseData = await response.json();

    if (!response.ok) {
      //error
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
  }
};
```

```
get으로 가져온 coach list를 for문을 돌면서 현재 어플리케이션에 적용되어잇는 key value에 맞춰 새로 object를 만들어 배열에 
넣어 저장한다. 이제 coach list를 사용하는 CoachesList component에서 refresh나, 새로이 페이지에 들어올 때 [ create ] 
store에서 fetch를 통해 get 한다.
```

```vue
// CoachesList.vue

<template>
  <section>
    <coach-filter @change-filter='setFilter'></coach-filter>
  </section>
  <section>
    <base-card>
      <div class='controls'>
        <base-button mode='outline' @click='loadCoaches'>Refresh</base-button>                        //
        <base-button v-if='!isCoach' link to='/register'>Register as Coach</base-button>
      </div>
      <ul v-if='hasCoaches'>
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

export default {
  name: 'CoachesList',
  components: { CoachItem, CoachFilter },
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
      return this.$store.getters['coaches/hasCoaches']
    },
    isCoach() {
      return this.$store.getters['coaches/isCoach']
    }
  },
  data() {
    return {
      activeFilters: {
        frontend: true,
        backend: true,
        career: true,
      }
    }
  },
  created() {                                        //
    this.loadCoaches();                              //
  },
  methods: {
    setFilter(updatedFilters) {
      this.activeFilters = updatedFilters;
    },

    loadCoaches() {                                //
      this.$store.dispatch('coaches/loadCoaches');  //
    }
  }
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
현재 이렇게만 작성 시 dummy 데이터와 겹쳐서 가져올 당시 차이로 인해 깜박임이 발생하거나, dummy data가 보였다가 
사라진다.
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21879974#content
