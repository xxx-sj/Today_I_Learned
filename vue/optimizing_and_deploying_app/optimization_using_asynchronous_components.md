# optimization: using asynchronous components

```
브라우저는 우리가 작성한 코드를 다운로드 받아 해석하여 클라이언트에게 보여주기 때문에 
작성한 코드가 적을수록 성능면에서 좋다. 그래서 중복코드라던가, 필요하지 않은 코드를 리팩토링하는 것이 좋다.
따라서 사용자가 웹사이트를 방문했을 때는 더 적은 코드를 다운로드 해야 일찍 페이지를 볼 수 있다.

import문이 있을 때마다 vue에게 해당 파일을 필요하다하여 가져오게 될것이다. 결국 이러한 코드들이 프로젝트를 build할 때 기본적으로
해당 파일들을 merge 한다. 만약, 우리가 필요로 할 때만 import 하여 vue가 download하게 만든다면 속도는 향상될 것이다.
컴포넌트 중에서도 사전에 로드되는 것이 아닌 필요할 때만 로드되도록하여 코드수를 줄일 수 있다. [we need it, not in advance,]
그 중 하나인 component가 dialog이다.
```

```
vue의 defineAsyncComponent를 사용하여 필요할 때 vue가 import하게 만들 수 있다.
```

```js
const BaseDialog = defineAsyncComponent(() => import());
```

```
import function을 이용한다. import문을 선언한것과는 다르게 js에서 제공하는 import func 이다.
```

```js
//main.js

import { createApp, defineAsyncComponent } from 'vue';

import router from '@/router';
import store from '@/store';
import App from '@/App';
import BaseCard from '@/components/UI/BaseCard';
import BaseButton from '@/components/UI/BaseButton';
import BaseBadge from '@/components/UI/BaseBadge';
import BaseSpinner from '@/components/UI/BaseSpinner'
// import BaseDialog from '@/components/UI/BaseDialog';

const BaseDialog = defineAsyncComponent(() => import('@/components/UI/BaseDialog'));

const app = createApp(App)

app.use(router)
app.use(store)

app.component('base-card', BaseCard)
app.component('base-button', BaseButton)
app.component('base-badge', BaseBadge)
app.component('base-spinner', BaseSpinner)
app.component('base-dialog', BaseDialog)

app.mount("#app");
```

```
이렇게 작성하게 되면 vue는 이제 현재 작업중인 component의 template를 체크하여 template중 사용하지 않는
component 같은 경우 다운로드하지 않는다. [ 하위컴포넌트 포함 ]
```

```
router에서도 동일하게 적용할 수 있다. router에 선언된 component를 모두 load하지 않고, 필요할 때[해당 페이지에 접근했을 때]
load되도록 만든다.
```

```js
//router.js

import {defineAsyncComponent} from 'vue'
import { createRouter, createWebHistory } from 'vue-router'

// import CoachDetail from '@/pages/coaches/CoachDetail';
import CoachesList from '@/pages/coaches/CoachesList';
// import CoachRegistration from '@/pages/coaches/CoachRegistration';

// import ContactCoach from '@/pages/requests/ContactCoach';
// import RequestsReceived from '@/pages/requests/RequestsReceived';
import NotFound from '@/pages/NotFound';
// import UserAuth from '@/pages/auth/UserAuth';
import store from './store/index'

const CoachDetail = defineAsyncComponent(() => import('@/pages/coaches/CoachDetail'));
const CoachRegistration = defineAsyncComponent(() => import('@/pages/coaches/CoachRegistration'));
const ContactCoach = defineAsyncComponent(() => import('@/pages/requests/ContactCoach'));
const RequestsReceived = defineAsyncComponent(() => import('@/pages/requests/RequestsReceived'));
const UserAuth = defineAsyncComponent(() => import('@/pages/auth/UserAuth'));

const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', redirect: '/coaches' },
    { path: '/coaches', component: CoachesList },
    { path: '/coaches/:id', props:true, component: CoachDetail, children: [
        { path: 'contact', component: ContactCoach }, // /coaches/c1/contact
      ] },
    { path: '/register', component: CoachRegistration, meta: { requiresAuth: true} },
    { path: '/requests', component: RequestsReceived,  meta: { requiresAuth: true} },
    { path: '/auth', component: UserAuth,  meta: { requiresUnauth: true} },
    { path: '/:notFound(.*)', component: NotFound },

  ],
});

router.beforeEach((to, _, next) => {
  if (to.meta.requiresAuth && !store.getters['auth/isAuthenticated']) {
    next('/auth');
  } else if (to.meta.requiresUnauth && store.getters['auth/isAuthenticated']) {
    next('/coaches');
  } else {
    next();
  }
});

export default router;
```

![image](https://user-images.githubusercontent.com/62305110/211184299-dcf079cd-f01b-42f1-8f7f-c8922d270910.png)
```
이렇게 0부터 시작되는 js파일들 중 필요한 file 만 다운로드 (200 ok) 하게 된다.
```

## 주의
```
router에서는 비동기 구성 요소를 사용하는 것을 권장하지 않는다.
(v-if 등으로 작업 시 구성 요소가 조건부로 로드 될 수 있기 때문에) 아래와 같이 코드를 수정한다.
```

```js
const CoachDetail = () => import('@/pages/coaches/CoachDetail');
const CoachRegistration = () => import('@/pages/coaches/CoachRegistration');
const ContactCoach = () => import('@/pages/requests/ContactCoach');
const RequestsReceived = () => import('@/pages/requests/RequestsReceived');
const UserAuth = () => import('@/pages/auth/UserAuth');
```

```
동작은 이전과 같다.
```
![image](https://user-images.githubusercontent.com/62305110/211184358-45c4fc1a-426a-464c-9906-acd861ea95de.png)


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880088#content
