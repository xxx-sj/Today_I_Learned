# add coach to vuex

```
coachForm에서 등록한 정보를 vuex에 저장하기
```

```vue
//CoachForm.vue

<template>
  <form @submit.prevent='submitForm'>
    <div class='form-control'>
      <label for='firstname'>Firstname</label>
      <input type='text' id='firstname' v-model.trim='firstName'/>
    </div>
    <div class='form-control'>
      <label for='lastname'>Firstname</label>
      <input type='text' id='lastname' v-model.trim='lastName'/>
    </div>
    <div class='form-control'>
      <label for='description'>Firstname</label>
      <textarea id='description' rows='5' v-model.trim='description'/>
    </div>
    <div class='form-control'>
      <label for='rate'>Hourly Rate</label>
      <input type='number' id='rate' v-model.number='rate'/>
    </div>
    <div class='form-control'>
      <h3>Areas of Expertise</h3>
      <div>
        <input type='checkbox' id='frontend' value='frontend' v-model='areas'>
        <label for='frontend'>Frontend Development</label>
        <div>
          <input type='checkbox' id='backend' value='backend' v-model='areas'>
          <label for='backend'>Backend Development</label>
        </div>
        <div>
          <input type='checkbox' id='career' value='career' v-model='areas'>
          <label for='career'>Career Advsory</label>
        </div>
      </div>
    </div>
      <base-button>Register</base-button>
  </form>
</template>

<script>
export default {
  emits: ['save-data'],
  name: 'CoachForm',
  data() {
    return {
      firstName: '',
      lastName: '',
      description: '',
      rate: null,
      areas: [],
    };
  },
  methods: {
    submitForm() {
      const formData = {
        first: this.firstName,
        last: this.lastName,
        desc: this.description,
        rate: this.rate,
        areas: this.areas
      };

      this.$emit('save-data', formData); //
    }
  }
};
</script>

<style scoped>
.form-control {
  margin: 0.5rem 0;
}

label {
  font-weight: bold;
  display: block;
  margin-bottom: 0.5rem;
}

input[type='checkbox'] + label {
  font-weight: normal;
  display: inline;
  margin: 0 0 0 0.5rem;
}

input,
textarea {
  display: block;
  width: 100%;
  border: 1px solid #ccc;
  font: inherit;
}

input:focus,
textarea:focus {
  background-color: #f0e6fd;
  outline: none;
  border-color: #3d008d;
}

input[type='checkbox'] {
  display: inline;
  width: auto;
  border: none;
}

input[type='checkbox']:focus {
  outline: #3d008d solid 1px;
}

h3 {
  margin: 0.5rem 0;
  font-size: 1rem;
}

.invalid label {
  color: red;
}

.invalid input,
.invalid textarea {
  border: 1px solid red;
}
</style>
```

```vue
//CoachRegistation.vue

<template>
  <section>
    <base-card>
      <h2>Register as a coach now!</h2>
      <coach-form @save-data='saveData'/>
    </base-card>
  </section>
</template>

<script>
import CoachForm from '@/components/coaches/CoachForm';
export default {
  name: 'CoachRegistration',
  components: { CoachForm, },

  methods: {
    saveData(data) {
      this.$store.dispatch('coaches/registerCoach', data);
      this.$router.replace("/coaches");   //direct 하여 form history 없애기
    },
  },
};
</script>

```


```
vuex에서 바로 state의 값을 변경하는건 권장하지 않기 때문에 actions와 mutations를 작성한다.
```

```js
// actions.js


export default {
  registerCoach(context, data) {
    const coachData = {
      id: 'c3',
      firstName: data.first,
      lastName: data.last,
      description: data.desc,
      hourlyRate: data.rate,
      areas: data.areas,
    };

    context.commit('registerCoach', coachData);
  }
};
```

```js
//mutations.js

export default {
  registerCoach(state, payload) {
    state.coaches.push(payload);
  }
};
```

```
coach로 등록 시 register 버튼이 보이지 않게 설정하기
먼저 root store에 userId를 하드코딩하기 임의로
```

```js
//store/index.js

import { createStore } from 'vuex';
import coachesModule from './modules/coaches/index'

const store = createStore({
  modules: {
    coaches: coachesModule,
  },
  state() {
    return {
      userId: 'c3', //
    };
  },
  getters: {
    userId(state) {
      return state.userId; //
    }
  }
});

export default store;
```

```
기존에 coaches actions에서 id를 하드코딩해뒀는데, 해당 값을 rootGetters를 통해 가져온다
```

```js
// store/modules/coaches/actions.js

export default {
  registerCoach(context, data) {
    const coachData = {
      id: context.rootGetters.userId, //
      firstName: data.first,
      lastName: data.last,
      description: data.desc,
      hourlyRate: data.rate,
      areas: data.areas,
    };

    context.commit('registerCoach', coachData);
  }
};

```


```js
// store/modules/coach/getters.js

export default {
  coaches(state) {
    return state.coaches;
  },
  hasCoaches(state) {
    return state.coaches && state.coaches.length > 0;
  },
  isCoach(_, getters, _2, rootGetters) { //
    const coaches = getters.coaches;
    const userId = rootGetters.userId;
    return coaches.some(coach => coach.id === userId);
  }
};
```

```
isCoaches 를 통해 coach인지 판단한다. _ , _2는 rootGetters와 getters를 사용하기 위해
인자를 필요로 하여 작성하였다. 대신 사용하지 않아 언더스코어로 표기
마지막으로 list에서 register 버튼에 조건을 추가한다.
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
        <base-button mode='outline'>Refresh</base-button>
        <base-button v-if='!isCoach' link to='/register'>Register as Coach</base-button> //
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
      return this.$store.getters['coaches/hasCoaches'] //
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



##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
