# regist router

```
npm install --save vue-router vuex 설치
src 아래 router를 작성할 router.js 파일 생성
```

```js
// src/router.js

import { createRouter, createWebHistory } from 'vue-router'

const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', redirect: '/coaches' },
    { path: '/coaches', component: null },
    { path: '/coaches/:id', component: null, children: [
        { path: '/contact', component: null }, // /coaches/c1/contact
      ] },
    { path: '/register', component: null },
    { path: '/requests', component: null },
    { path: '/:notFound(.*)', component: null },

  ],
});

export default router;
```

[history 차이](https://github.com/xxx-sj/Today_I_Learned/blob/master/vue/vue_router/readme.md)
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture 
