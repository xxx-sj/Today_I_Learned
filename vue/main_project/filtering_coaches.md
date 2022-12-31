# filtering coaches

```
area에 따라 filter를 적용하는 component를 만든다.
filter component는 components/coaches 아래에 속한다.
```

```vue
//CoachFilter.vue

<template>
  <base-card>
    <h2> Find Your Coach</h2>
    <span class='filter-option'>
      <input type='checkbox' id='frontend' checked @change='setFilter'/>
      <label for='frontend'>Frontend</label>
    </span>
    <span class='filter-option'>
      <input type='checkbox' id='backend' checked @change='setFilter'/>
      <label for='backend'>backend</label>
    </span>
    <span class='filter-option'>
      <input type='checkbox' id='career' checked @change='setFilter'/>
      <label for='career'>career</label>
    </span>
  </base-card>
</template>

<script>
export default {
  emits: ['change-filter'],
  name: 'CoachFilter',
  data() {
    return {
      filters: {
        frontend: true,
        backend: true,
        career: true,
      }
    };
  },
  methods: {
    setFilter(event) {
      const inputId = event.target.id;
      const isActive = event.target.checked;
      const updateFilters = {
        ...this.filters,
        [inputId]: isActive,
      };
      this.filters = updateFilters;
      this.$emit('change-filter', updateFilters);
    },
  }
};
</script>

<style scoped>
h2 {
  margin: 0.5rem 0;
}

.filter-option {
  margin-right: 1rem;
}

.filter-option label,
.filter-option input {
  vertical-align: middle;
}

.filter-option label {
  margin-left: 0.25rem;
}

.filter-option.active label {
  font-weight: bold;
}
</style>
```

```
filter는 checkbox를 클릭햇을 때 호출되며, spread 연산을 통해 객체 복사 및 마지막에 inputId와 isActive를 통해
overried로 값을 덮어씌운다. 해당 filter값은 filter  component 외에도 상위인 coachesList에 전달되도록 emit을 작성한다.
```

```vue
//CoachesList.vue

<template>
  <section>
    <coach-filter @change-filter='setFilter'></coach-filter>  //
  </section>
  <section>
    <base-card>
      <div class='controls'>
        <base-button mode='outline'>Refresh</base-button>
        <base-button link to='/register'>Register as Coach</base-button>
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
    filteredCoaches() {  //
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
  methods: {
    setFilter(updatedFilters) {
      this.activeFilters = updatedFilters;
    },
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
emit으로 filter data를 전달받으면 list에서 methods setFilter를 호출하여 인자로 받은 값을 저장한다.
또한 computed에서 store에서 가져오는 데이터를 filter를 통해 걸러내어 보여준다.
```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
