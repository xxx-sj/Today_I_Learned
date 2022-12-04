# UI_component

```
기본적인 UI를 담당할 component를 생성한다.
다른 로직은 존재하지 않고, 모든 component에서 wrapping을 역할만 할 것이다.
wrapping 역할을 하기 위해 vue의 built-in 인 slot을 사용한다.
```

```vue
//BaseCade.vue

<template>
  <div>
    <slot></slot>
  </div>
</template>

<script>
export default {
  name: 'BaseCard'
};
</script>

<style scoped>
div {
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  padding: 1rem;
  margin: 2rem auto;
  max-width: 40rem;
}
</style>
```

```
이제 UI component를 사용할 component로 이동한다.
```

```vue
//LearningResource

<template>
  <li>
    <div>
      <header>
        <h3>{{ title }}</h3>
        <button>Delete</button>
      </header>
    </div>
    <p>{{ description }}</p>
    <nav>
      <a :href='link'>View Resource</a>
    </nav>
  </li>
</template>
```

```
이 전에는 li 하위에 div로 감싸서 나타냈지만 UI component를 사용하기 위해
li 아래 div를 없애고 base-card를 추가한다. UI component의 시작이 div 부터 이기도 하고,
div 안에 slot이 존재하기에 div 아래에 있는 header 부터 slot에 들어가게 된다.
UI component는 전역적으로 사용 될 수 있기에 전역 component로 등록한다.
```

```vue
//LearningResource

<template>
  <li>
    <base-card>
      <header>
        <h3>{{ title }}</h3>
        <button>Delete</button>
      </header>
      <p>{{ description }}</p>
      <nav>
        <a :href='link'>View Resource</a>
      </nav>
    </base-card>
  </li>
</template>

<script>
import BaseCard from '@/components/UI/BaseCard';
export default {
  name: 'LearningResource',
  components: { BaseCard },
  props: ['title', 'description', 'link'],
};
</script>

<style scoped>
li {
  margin: auto;
  max-width: 40rem;
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

h3 {
  font-size: 1.25rem;
  margin: 0.5rem 0;
}

p {
  margin: 0.5rem 0;
}

a {
  text-decoration: none;
  color: #ce5c00;
}

a:hover,
a:active {
  color: #c89300;
}
</style>
```

```js
//main.js

import { createApp } from 'vue';
import App from '@/App';
import BaseCard from '@/components/UI/BaseCard';

const app = createApp(App);

app.component('base-card', BaseCard);

app.mount('#app');
```

### Header component 추가하기

```
앱에서 header는 한가지만 존재하게 만들것이므로 component name은 TheHeader로 지었다.
```

```vue
//TheHeader.vue

<template>
  <header>
    <h1>{{ title }}</h1>
  </header>
</template>

<script>
export default {
  name: 'TheHeader',
  props: ['title'],
};
</script>

<style scoped>
header {
  width: 100%;
  height: 5rem;
  background-color: #640032;
  display: flex;
  justify-content: center;
  align-items: center;
}

header h1 {
  color: white;
  margin: 0;
}
</style>
```

```vue
//App.vue

<template>
  <the-header title='RememberMe'></the-header> //
  <stored-resources :resources='storedResources'/>
</template>

<script>
import StoredResources from '@/components/learning-resources/StoredResources';
import TheHeader from '@/components/layouts/TheHeader';
export default {
  name: 'App',
  components: {
    TheHeader,
    StoredResources,
  },

  data() {
    return {
      storedResources: [
        { id: 'official-guide',
          title: 'Official Guide',
          description: 'The official Vue.js documentation',
          link: 'https://vuejs.org'
        },
        { id: 'google',
          title: 'Google',
          description: 'Learn to google...',
          link: 'https://google.com'
        },
      ],
    };
  },
};
</script>

```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
