# creating and using


```
npm install --save vuex 를 통해 project 내부에만 vuex를 설치했다면 App[vue instance] 에서 vuex를 사용할 수 있도록
등록해준다.
```

```
vuex에서 method [createStore]를 가져와 store를 만드는데 해당 메소드는 인자로 option object를 받는다.
```

```js
//main.js

import { createApp } from 'vue';
import { createStore } from 'vuex';

import App from './App.vue';

const store = createStore({
  state() {
    return {
      counter: 0
    };
  }
});

const app = createApp(App);

app.use(store);

app.mount('#app');
```

```
state는 메소드로 state object를 return해야한다. state object는 application 의 state이다.
vuex를 등록하는 것 [use]은 이전 router를 등록하는 것과 같다.
vuex에 등록한 state는 any component에서 사용가능하다.
그러면 어떻게 사용하는가?
```

```
$store를 통해 vuex store 값에 접근할 수 있다.
```

```vue
//App.vue

<template>
  <base-container title="Vuex">
    <h3>{{$store.state.counter}}</h3> //
    <button>Add 1</button>
  </base-container>
</template>
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
