# create base components

```
ui에 사용될 component들을 만든다. component 폴더 하위에 UI폴더를 생성하여 만드며, slot을 통해 모든 컨텐츠가
포함되도록 작성한다. 또한 UI는 모든 component에서 사용되므로 전역으로 등록해준다.
```
![image](https://user-images.githubusercontent.com/62305110/210124233-89378159-0c2c-4cf9-ab7b-7c7f5f0cf244.png)

```
container 역할을 하는 base-card이다.
```

```vue
//Base-card.vue

<template>
  <div class='card'>
    <slot></slot>
  </div>
</template>

<script>
export default {
  name: 'BaseCard'
};
</script>

<style scoped>
.card {
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  padding: 1rem;
  margin: 2rem auto;
  max-width: 40rem;
}
</style>
```

```vue
//CoachesList.vue

<template>
  <section>
    FILTER
  </section>
  <section>
    <base-card>                                                        //
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
    </base-card>
  </section>
</template>

<script>
import { mapGetters } from 'vuex'
import CoachItem from '@/components/coaches/CoachItem';
import BaseCard from '@/components/UI/BaseCard';

export default {
  name: 'CoachesList',
  components: { BaseCard, CoachItem },
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

```
전체적으로 사용될 button이다. button에는 button과 router-link가 있는데, 
button은 일반적인 button의 형태를 나타내고, router-link는 클릭 시 해당 path로 이동하게 만들어주는 것이다
props로 mode, link, to를 받는데, mode는 버튼의 type을 변경할 때 사용하며, link는 button이 link 형태인지, 
아니면 button형태 인지를 나타낸다. to는 router-link로 이동할 때 이동할 path를 전달한다.
```
```vue
//base-button.vue

<template>
  <button v-if='!link' :class='mode'>
    <slot></slot>
  </button>
  <router-link v-else :to='to' :class='mode'>
    <slot></slot>
  </router-link>
</template>

<script>
export default {
  props: {
    mode: {
      type: String,
      required: false,
      default: null,
    },
    link: {
      type: Boolean,
      required: false,
      default: false,
    },
    to: {
      type: String,
      required: false,
      default: '/'
    }
  },
  name: 'BaseButton'
};
</script>

<style scoped>
button,
a {
  text-decoration: none;
  padding: 0.75rem 1.5rem;
  font: inherit;
  background-color: #3a0061;
  border: 1px solid #3a0061;
  color: white;
  cursor: pointer;
  border-radius: 30px;
  margin-right: 0.5rem;
  display: inline-block;
}

a:hover,
a:active,
button:hover,
button:active {
  background-color: #270041;
  border-color: #270041;
}

.flat {
  background-color: transparent;
  color: #3a0061;
  border: none;
}

.outline {
  background-color: transparent;
  border-color: #270041;
  color: #270041;
}

.flat:hover,
.flat:active,
.outline:hover,
.outline:active {
  background-color: #edd2ff;
}
</style>
```

```vue
//CoachesList.vue button 사용처

<template>
  <section>
    FILTER
  </section>
  <section>
    <base-card>
      <div class='controls'>
        <base-button mode='outline'>Refresh</base-button>  //
        <base-button link to='/register'>Register as Coach</base-button> //
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
      <base-button mode='outline' link :to='coachContactLink'>Contact</base-button>  //
      <base-button link :to='coachDetailsLink'>View Details</base-button>  //
    </div>
  </li>
</template>
```

```
base-button component에 link 만을 작성하게 되면 link="link" true가 된다는것을 명싱하자.
```

```
Base-badge는 list와 같은 곳에 적용된다. type을 받아서 해당 text를 css로 꾸며주게된다.
```

```vue
//Base-Badge.vue

<template>
  <span class='badge' :class='type'>
    {{ text }}
  </span>
</template>

<script>
export default {
  props: ['type', 'title'],
  name: 'BaseBadge',
  computed: {
    text() {
      return this.title.toUpperCase();
    },
  }
};
</script>

<style scoped>
.badge {
  background-color: #ccc;
  color: #252525;
  border-radius: 30px;
  padding: 0.5rem 1.5rem;
  display: inline-block;
  margin-right: 0.5rem;
}

.frontend {
  background-color: #3d008d;
  color: white;
}

.backend {
  background-color: #71008d;
  color: white;
}

.career {
  background-color: #8d006e;
  color: white;
}
</style>
```

```vue
// CoachItem.vue //사용되는 곳

<template>
  <li>
    <h3>{{ fullName }}</h3>
    <h4>${{ rate }}/hour</h4>
    <div>
      <base-badge v-for='area in areas' :key='area' :type='area' :title='area'/> //
    </div>
    <div class='actions'>
      <base-button mode='outline' link :to='coachContactLink'>Contact</base-button>
      <base-button link :to='coachDetailsLink'>View Details</base-button>
    </div>
  </li>
```


```js
//main.js

import { createApp } from 'vue';

import router from '@/router';
import store from '@/store';
import App from '@/App';
import BaseCard from '@/components/UI/BaseCard';
import BaseButton from '@/components/UI/BaseButton';
import BaseBadge from '@/components/UI/BaseBadge';

const app = createApp(App)

app.use(router)
app.use(store)

app.component('base-card', BaseCard)
app.component('base-button', BaseButton)
app.component('base-badge', BaseBadge)

app.mount("#app");
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
