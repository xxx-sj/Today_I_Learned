# structuring vuex code and files

```
main.js 파일에 store를 모두 작성하다보면 line이 길어지면 파일이 커질것이다. store 도 router 처럼 분리하여 관리하는것이
유지보수에도 용이하다.
```

```js
//store.js

import { createStore } from 'vuex';

const counterModule = {
  namespaced: true,
  state() {
    return {
      counter: 0,
    }
  },
  mutations: {
    increment(state) {
      state.counter = state.counter + 2;
    },

    increase(state, payload) {
      console.log(state);
      state.counter = state.counter + payload.value;
    },
  },
  actions: {
    increment(context) {
      setTimeout(function() {
        context.commit('increment')
      }, 2000);
    },
    increase(context, payload) {
      console.log(context);
      context.commit('increase', payload);
    },
  },
  getters: {
    testAuth(state) {
      return state.isLoggedIn;
    },
    finalCounter(state) {
      return state.counter * 3;
    },
    normalizedCounter(_, getters) {
      // const finalCounter = state.counter * 3;
      const finalCounter = getters.finalCounter;
      if (finalCounter < 0) {
        return 0;
      }
      if (finalCounter > 100) {
        return 100;
      }
      return finalCounter;
    },
  },
}

const store = createStore({
  modules: {
    numbers: counterModule
  },
  state() {
    return {
      isLoggedIn: false,
    };
  },
  mutations: {
    setAuth(state, payload) {
      state.isLoggedIn = payload.isAuth;
    },
  },
  actions: {
    login(context) {
      context.commit('setAuth', {isAuth: true});
    },
    logout(context) {
      context.commit('setAuth', {isAuth: false});
    },
  },

  getters: {
    userIsAuthenticated(state) {
      return state.isLoggedIn;
    }
  }
});

export default store;  //마지막에 작성한 store를 export 한다.
```

```js
//main.js

import { createApp } from 'vue';
import App from './App.vue';
import store from './store'

const app = createApp(App);
app.use(store);
app.mount('#app');
```

```
store.js 파일로 따로 store코드를 빼고 마지막에 store를 export한다. main.js 에서는 
store을 import 하여 use 한다.
```
![image](https://user-images.githubusercontent.com/62305110/210050162-97f9cd5c-d326-4bd6-838d-703a8dd4c34b.png)

```
store.js 파일 하나로 store를 모두 관리하면 좋겠지만, 어플리케이션이 점점 커짐에 따라 store.js 또한
main.js 처럼 파일이 커질 수가 있다. 여기서 다시하번 모듈별로 파일을 나눈다.
```

![image](https://user-images.githubusercontent.com/62305110/210050098-f708f380-21c1-4281-b28e-988dc2bb765a.png)

```
app.vue와 동일 level에 store 라는 폴더를 만든다. 해당 폴더에는 index.js 를 갖는데, index.js 에서는
main store creation logic이 있다. 그리고 하위 디렉토리로 각각의 store 별로 디렉토리를 생성한다. 만약,
store가 점점 많아진다면 store 하위 디렉토리에 modules라는 폴더를 만들어서 store 폴더를 모두 넣어 관리하면 좋다.
```


```js
//store/index.js

import { createStore } from 'vuex';

import rootMutations from './mutations';
import rootActions from './actions'
import rootGetters from './getters'

import counterModule from './modules/counter/index'

const store = createStore({
  modules: {
    numbers: counterModule
  },
  state() {
    return {
      isLoggedIn: false,
    };
  },
  mutations: rootMutations,
  actions: rootActions,
  getters: rootGetters,
});

export default store;
```

```js
// store/mutations.js

export default {
  setAuth(state, payload) {
  state.isLoggedIn = payload.isAuth;
  }
}
```

```js
// store/getters.js

export default  {
  userIsAuthenticated(state) {
    return state.isLoggedIn;
  }
}
```

```js
// store/actions.js

export default {
  login(context) {
    context.commit('setAuth', {isAuth: true});
  },
  logout(context) {
    context.commit('setAuth', {isAuth: false});
  },
}
```

```
index 파일에는 main.js 에서 import 하여 사용할 수 있게 export를 해주게 되며, 
mutaions, actions, getters를 분리하여 관리하게 된다. 각각의 분리된 methods은 export를 통해 해당 object를 반환하고
index는 그 ojbect 들을 사용한다. 만약 store의 크기가 작다면 actions, mutations, getters를 분리하지 않아도 된다.
```

```js
// store/modules/counter/index.js

import counterMutations from './mutations'
import counterActions from './actions'
import counterGetters from './getters'
export default {
  namespaced: true,
  state() {
    return {
      counter: 0,
    }
  },
  mutations: counterMutations,
  actions: counterActions,
  getters: counterGetters

};
```

```js
// store/modules/counter/mutations.js

export default {
  increment(state) {
    state.counter = state.counter + 2;
  },

  increase(state, payload) {
    console.log(state);
    state.counter = state.counter + payload.value;
  },
};
```

```js
// store/modules/counter/getters.js

export default {
  testAuth(state) {
    return state.isLoggedIn;
  },
  finalCounter(state) {
    return state.counter * 3;
  },
  normalizedCounter(_, getters) {
    // const finalCounter = state.counter * 3;
    const finalCounter = getters.finalCounter;
    if (finalCounter < 0) {
      return 0;
    }
    if (finalCounter > 100) {
      return 100;
    }
    return finalCounter;
  },
};
```

```js
// store/modules/counter/actions.js

export default  {
  increment(context) {
    setTimeout(function() {
      context.commit('increment')
    }, 2000);
  },
  increase(context, payload) {
    console.log(context);
    context.commit('increase', payload);
  },
};
```

```
다른 모듈들도 마찬가지로 index에서는 main store 파일에서 사용할 수 있도록 index 파일에서 store object를 export 하며,
module index 파일에서 사용할 actions, getter, mutations를 분리할 수 있다. module도 동일하게 파일이 작다면 하나의 파일로
관리하는 것이 좋을 수 있다.
```

```
마지막으로 vue instance를 생성하는 main.js 파일에서 main store 파일을 import 하고 use 한다.
```

```js
import { createApp } from 'vue';
import App from './App.vue';
import store from './store/index';

const app = createApp(App);

app.use(store);

app.mount('#app');

```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
