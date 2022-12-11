# vue-router

```
npm을 통해 v-router를 설치하였다면, App에 router를 추가할 수 있다.
```

```js
//main.js

import { createApp } from 'vue';
import { createRouter } from 'vue-router';

import App from './App.vue';

const router = createRouter({});
const app = createApp(App)

app.mount('#app');
```

```
App을 초기화하는 부분인 main.js에서 createRouter 라는 method를 import 받아서 router 객체를 만든다.
router 생성 시 options을 전달하게 되어있는데, 하나는 routes라는 property이고 하나는 history property 이다.
```

```
routes는 router가 기억하고 url를 통해 이동할 routes 목록들이다. 값으로는 배열을 전달한다.
```

```
다른 값으로는 history가 있는데, 유저가 버튼이나 어떠한 이벤트에 의해 다른 페이지로 이동할 때 브라우저 history를 
추가하고 난 후, 사용자가 뒤로 가기 버튼을 클릭하면 [ 브라우저 뒤로가기 ] 브라우저가 기록한 이전 페이지로 이동하게 된다.
history의 값으로 vue-router로 부터 다른 하나의 function을 import 하게 되는데, function name은 createWebHistory 이다. 
브라우저의 자바스크립트는 브라우저의 메모리를 조작할 수 있는것은 아니다. [ 어디서 왔는지, 사용자가 어떤 페이지에 있는지 등..]
브라우저 URL 에 #이 붙으면 # 뒤에 붙은 URL에 대해 변화가 일어나도 브라우저는 인식을 할 수 없다. 대신 브라우저와 같은 매커니즘으로
vue-router가 인식을 하여 그에 맞는 component를 변경해주는 역할을 대신해주는 것이다.
```

#### 출처: https://blinders.tistory.com/76

```js
//main.js

import { createApp } from 'vue';
import { createRouter, createWebHistory } from 'vue-router';

import App from './App.vue';

const router = createRouter({
  history: createWebHistory(),
  routes: [],
});
const app = createApp(App)

app.mount('#app');
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
