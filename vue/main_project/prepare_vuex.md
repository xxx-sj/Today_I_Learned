# prepare vuex

```
fire-base에서는 authentication tab에 들어가서 email login을 활성화 해준다.
```
![image](https://user-images.githubusercontent.com/62305110/210830440-0165a37b-0934-4533-99f5-8e7df6d80701.png)

```
이 후 fire-base rest-api를 들어가 보면 Sign up email/paasword, Sign in with email/password api를 찾을 수 있다.
기존에 root store index에 있던 auth code들은 모두 store/ 아래 moduele/auth를 새로 만들어서 작성한다.
```
![image](https://user-images.githubusercontent.com/62305110/210832233-337edb72-fd9f-41c4-9cc0-47f6738c3f34.png)


```js
index.js

import mutations from './mutations'
import actions from './actions'
import getters from './getters'
export default {
  namespaced: true,
  state() {
    return {
      userId: 'c1',
    };
  },
  mutations,
  getters,
  actions,
}
```

```js
actions.js

export default {}
```

```js
getters.js

export default {
  userId(state) {
    return state.userId;
  }
}
```

```js
mutations.js

export default {};
```

```js
root index.js

import { createStore } from 'vuex';

import coachesModule from './modules/coaches/index'
import requestsModule from './modules/requests/index'
import authModule from './modules/auth/index'

const store = createStore({
  modules: {
    coaches: coachesModule,
    requests: requestsModule,
    auth: authModule,
  }
});

export default store;

```



##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880016#content
