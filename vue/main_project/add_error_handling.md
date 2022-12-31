# add error handling


```
error에 대한 dialog 추가하기 component/UI 에 basedialog 추가하기
```

```vue
//BaseDialog.vue

<template>
  <teleport to="body">
    <div v-if="show" @click="tryClose" class="backdrop"></div>
    <dialog open v-if="show">
      <header>
        <slot name="header">
          <h2>{{ title }}</h2>
        </slot>
      </header>
      <section>
        <slot></slot>
      </section>
      <menu v-if="!fixed">
        <slot name="actions">
          <base-button @click="tryClose">Close</base-button>
        </slot>
      </menu>
    </dialog>
  </teleport>
</template>

<script>
export default {
  props: {
    show: {
      type: Boolean,
      required: true,
    },
    title: {
      type: String,
      required: false,
    },
    fixed: {
      type: Boolean,
      required: false,
      default: false,
    },
  },
  emits: ['close'],
  methods: {
    tryClose() {
      if (this.fixed) {
        return;
      }
      this.$emit('close');
    },
  },
};
</script>

<style scoped>
.backdrop {
  position: fixed;
  top: 0;
  left: 0;
  height: 100vh;
  width: 100%;
  background-color: rgba(0, 0, 0, 0.75);
  z-index: 10;
}

dialog {
  position: fixed;
  top: 20vh;
  left: 10%;
  width: 80%;
  z-index: 100;
  border-radius: 12px;
  border: none;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  padding: 0;
  margin: 0;
  overflow: hidden;
  background-color: white;
}

header {
  background-color: #3a0061;
  color: white;
  width: 100%;
  padding: 1rem;
}

header h2 {
  margin: 0;
}

section {
  padding: 1rem;
}

menu {
  padding: 1rem;
  display: flex;
  justify-content: flex-end;
  margin: 0;
}

@media (min-width: 768px) {
  dialog {
    left: calc(50% - 20rem);
    width: 40rem;
  }
}
</style>
```


```
error가 발생했을 때 적용하기 coach list에서 가져오지 못했을 때 
```

```vue
//CoachesList.vue

<template>
  <base-dialog :show='!!error' title='An error occurred!' @close='handleError'>  //
    <p>{{ error }}</p>
  </base-dialog>
  <section>
    <coach-filter @change-filter='setFilter'></coach-filter>
  </section>
  <section>
    <base-card>
      <div class='controls'>
        <base-button mode='outline' @click='loadCoaches'>Refresh</base-button>
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
    this.loadCoaches();
  },
  methods: {
    setFilter(updatedFilters) {
      this.activeFilters = updatedFilters;
    },

    async loadCoaches() {
      this.isLoading = true;
      try {
        await this.$store.dispatch('coaches/loadCoaches');  //
      } catch(error) {
        this.error = error.message || 'Something went wrong!';  //
      }
      this.isLoading = false;

    },
    handleError() {
      this.error = null;  //
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
fetch에서 error가 발생했을 때 data error에 error message를 넣어준다. error가 있을 경우 dialog를 보여주게 되며
close할 때 show의 조건이 error를 null 로 초기화 한다. dialog는 teleport를 통해 body에 렌더링되고 있다.
```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21879982#content
