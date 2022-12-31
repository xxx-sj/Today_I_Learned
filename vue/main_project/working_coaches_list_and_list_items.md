# working on coaches list and list item

```
coach 리스트의 하나의 아이템을 추가하기 위해 components 아래에 coaches directory를 만든 후 item을 추가한다.
```

```vue
//CoachItem.vue

<template>
  <li>
    <h3>{{ fullName }}</h3>
    <h4>${{ rate }}/hour</h4>
    <div>
      <span v-for='area in areas' :key='area'>{{ area }}</span>
    </div>
    <div class='actions'>
      <router-link :to='coachContactLink'>Contact</router-link>
      <router-link :to='coachDetailsLink'>View Details</router-link>
    </div>
  </li>
</template>

<script>
export default {
  props: ['id', 'firstName', 'lastName', 'rate', 'areas'],
  name: 'CoachItem',
  computed: {
    fullName() {
      return `${this.firstName} ${this.lastName}`
    },
    coachContactLink() {
      // return `/coaches/${this.id}/contact` //coaches/c1/contact
      return `${this.$route.path}/${this.id}/contact` //coaches/c1/contact
    },
    coachDetailsLink() {
      // return `/coaches/${this.id}`; // /coaches/c1
      return `${this.$route.path}/${this.id}`; // /coaches/c1
    }
  },

};
</script>

<style scoped>
li {
  margin: 1rem 0;
  border: 1px solid #424242;
  border-radius: 12px;
  padding: 1rem;
}

h3 {
  font-size: 1.5rem;
}

h3,
h4 {
  margin: 0.5rem 0;
}

div {
  margin: 0.5rem 0;
}

.actions {
  display: flex;
  justify-content: flex-end;
}
</style>
```

```
item은 하나의 row이므로 모든 데이터를 나타내고 있으며, props로 데이터를 받는 이유는 list에서 for를 통해 데이터를 넘겨 주는데
for문을 통한 데이터는 local 데이터이기때문이다. 몇가지 computed 속성을 추가해주고,  router-link를 하드코딩해도 되지만 
route가 변경되면 모든 곳을 다 변경해줘야하는 번거로움이 있다. 그러한 문제를 해결하기 위해서 [ this.$route.path ]를 통해 현재 path 정보를
갖고 path를 추가하여 작성한다.
```

```
이제 item component가 작성되었으니 list에서 div 부분을 coach-item으로 변경한다.
```

```vue
//CoachesList.vue

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
      <coach-item v-for='coach in filteredCoaches' :key='coach.id'
      :id='coach.id'
      :first-name='coach.firstName'
                  :last-name='coach.lastName'
                  :rate='coach.hourlyRate'
                  :areas='coach.areas'
      />
    </ul>
    <h3 v-else>No caches Found.</h3>
  </section>
</template>

<script>
import { mapGetters } from 'vuex'
import CoachItem from '@/components/coaches/CoachItem';

export default {
  name: 'CoachesList',
  components: { CoachItem },
  computed: {
    ...mapGetters('coaches', {
      filteredCoaches: 'coaches',
      hasCoaches: 'hasCoaches'
    })
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
![image](https://user-images.githubusercontent.com/62305110/210123849-bd86126d-0ea0-43c2-a416-2f15f5c29e10.png)

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
