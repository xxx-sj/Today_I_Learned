# animation with vue-router

```
vue-router에 대한 animation을 추가하기전에 기본적인 vue-router 데이터를 추가한다.
1. npm install --save vue-router 를 통해 해당 프로젝트에 대해서만 vue-router를 install 한다.
```

```
vue-router 파일을 만들거나 main.js에 router에 대한 정보를 정의한다.
```

```js
//main.js

import { createApp } from 'vue';
import {createRouter, createWebHistory} from 'vue-router'

import App from './App.vue';
import BaseModal from './components/BaseModal.vue';
import AllUsers from '@/pages/AllUsers';
import CourseGoals from '@/pages/CourseGoals';

const router = createRouter({
  history: createWebHistory(),
  routes: [
    {path: '/', component: AllUsers},
    {path: '/goals', component: CourseGoals},
  ]
})

const app = createApp(App);

app.component('base-modal', BaseModal);
app.use(router); // vue-router 사용
app.mount('#app');
```

```
이 후에 App.vue 에서는 기존에 template에 정의했던 elements를 삭제하고 router-view만을 정의한다.
```

```vue
//App.vue

<template>
<router-view></router-view>
</template>

<script>
export default {
...
}
```

```
vue-router를 통해 animation을 구현할 2개의 component를 정의한다.
```

```vue
//AllUser.vue

<template>
  <div class='container'>
    <h2>All Users</h2>
    <router-link to='/goals'>All Users</router-link>
  </div>
</template>

<script>
export default {
  name: 'AllUsers'
};
</script>
```

```vue
//CourseGoals.vue

<template>
  <div class='container'>
    <h2>Course Goals</h2>
    <router-link to='/'>Course Goals</router-link>
  </div>
</template>

<script>
export default {
  name: 'CourseGoals'
};
</script>
```

```
2 개의 component를 이전과 동일하게 transition tag 를 통해 애니메이션을 정의할 수 있다.
```

```vue
//App.vue

<template>
  <transition name='route'> //
    <router-view></router-view>
  </transition>
</template>

<script>
export default {
};
</script>

<style>
* {
  box-sizing: border-box;
}
html {
  font-family: sans-serif;
}
body {
  margin: 0;
}
button {
  font: inherit;
  padding: 0.5rem 2rem;
  border: 1px solid #810032;
  border-radius: 30px;
  background-color: #810032;
  color: white;
  cursor: pointer;
}
button:hover,
button:active {
  background-color: #a80b48;
  border-color: #a80b48;
}
.block {
  width: 8rem;
  height: 8rem;
  background-color: #290033;
  margin-bottom: 2rem;
  /*transition: transform 0.3s ease-out;*/

}
.container {
  max-width: 40rem;
  margin: 2rem auto;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  padding: 2rem;
  border: 2px solid #ccc;
  border-radius: 12px;
}
.animate {
  /*transform: translateX(-150px);*/
  animation: slide-fade 0.3s ease-out forwards;
}

.fade-button-enter-from,
.fade-button-leave-to{
  opacity: 0;
}

.fade-button-enter-active {
  transition: opacity 0.3s ease-out;
}
.fade-button-leave-active {
  transition: opacity 0.3s ease-in;
}

.fade-button-enter-to,
.fade-button-leave-from{
  opacity: 1;
}

.route-enter-from{} //
.route-enter-active{
  animation: slide-scale 0.4s ease-out; //
}
.route-enter-to{} //

.route-leave-active {
  animation: slide-scale 0.4s ease-in; //
}
</style>
```

```
하지만 vue에서는 아래와 같은 warn을 제공한다.
```
![image](https://user-images.githubusercontent.com/62305110/208476200-e7b5a447-541d-43ff-bd5b-01eba9062009.png)

```
이는 router-view는 더이상 직접적으로 내부에 transition 또는 keep-alive 를 사용할 수 없다 [ Vue3 부터 ]
대신 위의 이미지에서 대체 코드를 찾을 수 있다.

transition이 router-view에 들어가는 것이다. 대신 실제 페이지 [ router-view가 렌더링되야할 곳을 알려줘야 한다.
```

```vue
//App.vue

<template>
  <router-view v-slot='slotProps'>
    <transition name='fade-button' mode='out-in'>
      <component :is='slotProps.Component' />
    </transition>
  </router-view>
</template>
```

```
router-view에 v-slot을 선언하면 props를 받고, transition를 감싼 부분에 component를 선언하고 slotPops에 Component라는
[현재 선택된 경로에 대해 로드되어야 하는]값을 전달하면서 해당 component를 렌더링하게 된다.
하지만 이것에도 문제가 있는데 두개의 animation이 발생되면서 한 화면에 animation이 동작하면서 2개의 component가 나타나게된다.
```
![image](https://user-images.githubusercontent.com/62305110/208477805-5cc207f5-240f-4089-a3c6-541c8be56b02.png)

```
이러한 문제를 해결하기 위해 간단한 방법은 transition tag에 mode라는 속성에 [ out-in ] 을 추가하는 것이다.
out-in 속성은 이전 애니메이션이 끝나면 이 후 애니메이션이 동작하게 하는 속성이다.
```

```
route animation에서 수정하고 나서 reload할 때 animation이 동작할 때가 있는것을 확인 할 수 있는데, 이 문제는
초기 애니메이션이 있기 때문이다. 이유는 vue-router가 기술적으로 경로의 첫 번째 작업의 경로는 빈 경로 "/" 이기 때문이다.
이 후 페이지가 시작되면 [ 로드되면 ] 사용해야할 경로로 즉시 전환된다. 즉, 앱이 시작될 때 vue-router는 경로가 없다가 주어진 경로에 대해
선택한 경로를 따라서 애니메이션을 재생한다.

이 동작을 피하기 위해 main.js [ vue-router를 사용하는] 곳에서 router.isReady 의 callback를 통해 처리할 수 있다.
이 메소드는 라우터가 로드해야 하는 페이즈를 성공적으로 evaluated 하고나서 호출되는 method 이다. isReady는 promise를 반환한다.
```

```js
//main.js

const router = createRouter({
  history: createWebHistory(),
  routes: [
    {path: '/', component: AllUsers},
    {path: '/goals', component: CourseGoals},
  ]
})

const app = createApp(App);

app.component('base-modal', BaseModal);

app.use(router);

router.isReady().then(function() {
  app.mount('#app');
});
```

```
router는 더이상 빈 페이지가 아닌 로드되어야 하는 페이지를 알고 렌더링하게 된다.
```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
