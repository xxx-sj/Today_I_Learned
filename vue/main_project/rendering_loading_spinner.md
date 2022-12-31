# rendering loading spinner

```
async를 사용하면 자동으로 promise를 반환한다.
```

```vue
//CoachesList.vue

<template>
  <section>
    <coach-filter @change-filter='setFilter'></coach-filter>
  </section>
  <section>
    <base-card>
      <div class='controls'>
        <base-button mode='outline' @click='loadCoaches'>Refresh</base-button>
        <base-button v-if='!isCoach && !isLoading' link to='/register'>Register as Coach</base-button>       //
      </div>
      <div v-if='isLoading'>              //
        <base-spinner />                 //
      </div>                             //
      <ul v-else-if='hasCoaches'>         //
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

export default {
  name: 'CoachesList',
  components: { BaseSpinner, CoachItem, CoachFilter },
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
      return !this.isLoading && this.$store.getters['coaches/hasCoaches']            //
    },
    isCoach() {
      return this.$store.getters['coaches/isCoach']
    }
  },
  data() {
    return {
      isLoading: false,                       //
      activeFilters: {
        frontend: true,
        backend: true,
        career: true,
      }
    }
  },
  created() {
    this.loadCoaches();
  },
  methods: {
    setFilter(updatedFilters) {
      this.activeFilters = updatedFilters;
    },

    async loadCoaches() {                                      //
      this.isLoading = true;                                   //
      const a = await this.$store.dispatch('coaches/loadCoaches');           //
      console.log("a", a);                                      
      this.isLoading = false;                           //

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
this.$store.dispatch('coaches/loadCoaches'); code가 내부에서 async await를 사용하고 있어
this.$store.dispatch('coaches/loadCoaches');를 call하기 전에 isLoading으로 spinner를 보여주었다가 
async / await 을 통해 데이터를 받고 set 하고나면, spinner를 감춘다. 
coach list 와 button에 조건을 수정하여 this.$store.dispatch('coaches/loadCoaches') method가 끝나기 전까지는
spinner만 보여주게 된다.
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21879980#content
