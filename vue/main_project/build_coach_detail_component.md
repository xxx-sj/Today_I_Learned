# build coach detail component

```
이 전 hard coding 된 코드는 지우고, 새로 다시 작성하기
coach의 상세정보로 이름, rate, area 등등 작성될 content를 추가한다.
detail page는 router에서 /:id 를 통해 동적으로 페이지를 이동하는데 동적으로 전달받는
id값을 router에서 받기 위해 router를 설정한 route에서 props 속성을 true로 해주고 받으려고 하는
component 내에서 props를 통해 받을 수 있다.
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

const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', redirect: '/coaches' },
    { path: '/coaches', component: CoachesList },
    { path: '/coaches/:id', props:true, component: CoachDetail, children: [ //
        { path: 'contact', component: ContactCoach }, // /coaches/c1/contact
      ] },
    { path: '/register', component: CoachRegistration },
    { path: '/requests', component: RequestsReceived },
    { path: '/:notFound(.*)', component: NotFound },

  ],
});

export default router;
```


```vue
//CoachDetail.vue

<template>
  <section>
    <base-card>
      <h2>{{ fullName }}</h2>
      <h3>${{ rate }}/hour</h3>
    </base-card>
  </section>
  <section>
    <base-card>
      <heaer>
        <h2> Interested? Reach out now! </h2>
        <base-button link :to='contactLink'>Contact</base-button>
      </heaer>
      <router-view></router-view>
    </base-card>
  </section>
  <section>
    <base-card>
      <base-badge v-for='area in areas' :key='area' :type='area' :title='area' />
      <p>{{ description }}</p>
    </base-card>
  </section>
</template>

<script>
import BaseCard from '@/components/UI/BaseCard';
import BaseButton from '@/components/UI/BaseButton';
import BaseBadge from '@/components/UI/BaseBadge';
export default {
  props: ['id'],
  name: 'CoachDetail',
  data() {
    return {
      selectedCoach: null
    }
  },
  computed: {
    fullName() {
        return this.selectedCoach.firstName + " " + this.selectedCoach.lastName;
    },
    contactLink() {
      return this.$route.path + "/" + this.id + "/contact";
    }

  },
  created() {
    this.selectedCoach = this.$store.getters['coaches/coaches'].find(
      coach => coach.id === this.id
    );
  },
  components: { BaseBadge, BaseButton, BaseCard },

};
</script>
```

```
coach 의 데이터는 현재 vuex store에 저장되어 있어서 props로 전달받은 id를 갖고 vuex store에서 해당 
coach 정보를 얻는다. 만약 url에서 id를 사용자가 임의로 [ 없는 데이터 ] 로 접근했을 때 페이지의 데이터가 보여주지 않은 것에
대해서 처리할 방법을 생각해보기.
```

```vue
//CoachDetail.vue

<template>
  <section>
    <base-card>
      <h2>{{ fullName }}</h2>
      <h3>${{ rate }}/hour</h3>
    </base-card>
  </section>
  <section>
    <base-card>
      <header>
        <h2> Interested? Reach out now! </h2>
        <base-button link :to='contactLink'>Contact</base-button>
      </header>
      <router-view></router-view>
    </base-card>
  </section>
  <section>
    <base-card>
      <base-badge v-for='area in areas' :key='area' :type='area' :title='area' />
      <p>{{ description }}</p>
    </base-card>
  </section>
</template>

<script>
import BaseCard from '@/components/UI/BaseCard';
import BaseButton from '@/components/UI/BaseButton';
import BaseBadge from '@/components/UI/BaseBadge';
export default {
  props: ['id'],
  name: 'CoachDetail',
  data() {
    return {
      selectedCoach: null
    }
  },
  computed: {
    fullName() {
        return this.selectedCoach.firstName + " " + this.selectedCoach.lastName;
    },
    contactLink() {
      return this.$route.path + "/" + this.id + "/contact";
    },
    areas() {
      return this.selectedCoach.areas;
    },
    rate() {
      return this.selectedCoach.hourlyRate;
    },
    description() {
      return this.selectedCoach.description;
    }

  },
  created() {
    this.selectedCoach = this.$store.getters['coaches/coaches'].find(
      coach => coach.id === this.id
    );
  },
  components: { BaseBadge, BaseButton, BaseCard },

};
</script>
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
