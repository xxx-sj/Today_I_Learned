# adding auto logou

```
expireToken을 이용하여 자동 로그아웃을 구현한다. 
fire-base에 로그인을 시도하면 reponse로 로그인 시간 기준 1시간을 초로 나타낸 expireToken을 준다.
이 값을 이용하여 autoLogout을 구현한다. 먼저 logout을 할 때 login에서 사용한 token, userId를 
localstorage에서 remove한다.
```

```js
// auth/actions.js

let timer;

export default {
  async login(context, payload) {
    return context.dispatch('auth', {
      ...payload,
      mode: 'login',
    })
  },
  async signup(context, payload) {
    return context.dispatch('auth', {
      ...payload,
      mode: 'signup',
    })
  },

  async auth(context, payload) {
    const mode = payload.mode;
    let url = `https://identitytoolkit.googleapis.com/v1/accounts:signInWithPassword?key=AIzaSyAJLkNz9pbIzgj89yJwk3SXJW3MUuTp5L8`
    if (mode === 'signup') {
      url = `https://identitytoolkit.googleapis.com/v1/accounts:signUp?key=AIzaSyAJLkNz9pbIzgj89yJwk3SXJW3MUuTp5L8`
    }
    const response = await fetch(url, {
      method: 'POST',
      body: JSON.stringify({
        email: payload.email,
        password: payload.password,
        returnSecureToken: true,
      })
    });

    const responseData = await response.json();

    if (!response.ok) {
      const error = new Error(responseData.message || 'Failed to authenticate. check your login data');
      throw error;
    }

    localStorage.setItem('token', responseData.idToken);
    localStorage.setItem('userId', responseData.localId);

    context.commit('setUser', {
      token: responseData.idToken,
      userid: responseData.localId,
      tokenExpiration: expirationDate,
    });
  },

  tryLogin(context) {
    const token = localStorage.getItem('token');
    const userId = localStorage.getItem('userId');

    if (token && userId) {
      context.commit('setUser', {
        token: token,
        userId: userId,
        tokenExpiration: null,
      })
    }

  },

  logout(context) {
    localStorage.removeItem('token');                    //
    localStorage.removeItem('userId');                   //

    context.commit('setUser', {
      token : null,
      userId : null,
      tokenExpiration : null,
    });
  },
}
```

```
다음으로 로그인을 시도하여 reponse로 얻은 expiresIn 을 이용하여 해당값을 ms로 변경한 후 ms로 나타낸 현재 시간에 더하여
이 후 만료될 시간을 구한다.
```

```js
// auth/actions.js


let timer;

export default {
  async login(context, payload) {
    return context.dispatch('auth', {
      ...payload,
      mode: 'login',
    })
  },
  async signup(context, payload) {
    return context.dispatch('auth', {
      ...payload,
      mode: 'signup',
    })
  },

  async auth(context, payload) {
    const mode = payload.mode;
    let url = `https://identitytoolkit.googleapis.com/v1/accounts:signInWithPassword?key=AIzaSyAJLkNz9pbIzgj89yJwk3SXJW3MUuTp5L8`
    if (mode === 'signup') {
      url = `https://identitytoolkit.googleapis.com/v1/accounts:signUp?key=AIzaSyAJLkNz9pbIzgj89yJwk3SXJW3MUuTp5L8`
    }
    const response = await fetch(url, {
      method: 'POST',
      body: JSON.stringify({
        email: payload.email,
        password: payload.password,
        returnSecureToken: true,
      })
    });

    const responseData = await response.json();

    if (!response.ok) {
      const error = new Error(responseData.message || 'Failed to authenticate. check your login data');
      throw error;
    }

    // const expiresIn = +responseData.expiresIn * 1000;                  // 1
    const expiresIn = 5000; // test                                       // 1
    const expirationDate = new Date().getTime() + expiresIn;              // 1

    localStorage.setItem('token', responseData.idToken);
    localStorage.setItem('userId', responseData.localId);
    localStorage.setItem('tokenExpiration', expirationDate);               //1

    timer = setTimeout(() => {
      context.dispatch('autoLogout');
    }, expiresIn);

    context.commit('setUser', {
      token: responseData.idToken,
      userid: responseData.localId,
      // tokenExpiration: expirationDate,                                    //2
    });
  },

  tryLogin(context) {
    const token = localStorage.getItem('token');
    const userId = localStorage.getItem('userId');
    const tokenExpiration = localStorage.getItem('tokenExpiration');          

    const expiresIn = +tokenExpiration - new Date().getTime();               

    // if (expiresIn <= 10000) {      
    if (expiresIn <= 0) {
      return;
    }

    setTimeout(() => {
      context.dispatch('autoLogout');
    }, expiresIn)

    if (token && userId) {
      context.commit('setUser', {
        token: token,
        userId: userId,
        // tokenExpiration: null,                                         //2
      })
    }

  },

  logout(context) {
    localStorage.removeItem('token');
    localStorage.removeItem('userId');
    localStorage.removeItem('tokenExpiration');

    clearTimeout(timer);

    context.commit('setUser', {
      token : null,
      userId : null,
      // tokenExpiration : null,
    });
  },

  autoLogout(context) {
    context.dispatch('logout');
    context.commit('setAutoLogout');
  }
}
```

```
그렇게 ms를 더하여 구한 값[expirationDate]을 localStorage에 저장한다 [1]. 이제 만료 토큰 값은 localstorage에서 꺼내 쓰기 때문에
vuex/store에서 tokenExpiration 값을 저장할 필요가 없어진다. [2]
```

```js
// auth/action.js

let timer;                                           //1

export default {
  async login(context, payload) {
    return context.dispatch('auth', {
      ...payload,
      mode: 'login',
    })
  },
  async signup(context, payload) {
    return context.dispatch('auth', {
      ...payload,
      mode: 'signup',
    })
  },

  async auth(context, payload) {
    const mode = payload.mode;
    let url = `https://identitytoolkit.googleapis.com/v1/accounts:signInWithPassword?key=AIzaSyAJLkNz9pbIzgj89yJwk3SXJW3MUuTp5L8`
    if (mode === 'signup') {
      url = `https://identitytoolkit.googleapis.com/v1/accounts:signUp?key=AIzaSyAJLkNz9pbIzgj89yJwk3SXJW3MUuTp5L8`
    }
    const response = await fetch(url, {
      method: 'POST',
      body: JSON.stringify({
        email: payload.email,
        password: payload.password,
        returnSecureToken: true,
      })
    });

    const responseData = await response.json();

    if (!response.ok) {
      const error = new Error(responseData.message || 'Failed to authenticate. check your login data');
      throw error;
    }

    // const expiresIn = +responseData.expiresIn * 1000;
    const expiresIn = 5000; // test
    const expirationDate = new Date().getTime() + expiresIn;

    localStorage.setItem('token', responseData.idToken);
    localStorage.setItem('userId', responseData.localId);
    localStorage.setItem('tokenExpiration', expirationDate);

    timer = setTimeout(() => {                                      // 1
      context.dispatch('logout');                                   // 1
    }, expiresIn);                                                  // 1

    context.commit('setUser', {
      token: responseData.idToken,
      userid: responseData.localId,
      // tokenExpiration: expirationDate,
    });
  },

  tryLogin(context) {
    const token = localStorage.getItem('token');
    const userId = localStorage.getItem('userId');
    const tokenExpiration = localStorage.getItem('tokenExpiration');          //3
 
    const expiresIn = +tokenExpiration - new Date().getTime();                //3

    // if (expiresIn <= 10000) {                                              //3
    if (expiresIn <= 0) {                                                     //3
      return;                                                                 //3
    }                                                                         //3
 
    setTimeout(() => {                                                        //3
      context.dispatch('logout');                                             //3
    }, expiresIn)                                                             //3

    if (token && userId) {
      context.commit('setUser', {
        token: token,
        userId: userId,
        // tokenExpiration: null,
      })
    }

  },

  logout(context) {
    localStorage.removeItem('token');
    localStorage.removeItem('userId');
    localStorage.removeItem('tokenExpiration');                 //2

    clearTimeout(timer);                                        //2

    context.commit('setUser', {
      token : null,
      userId : null,
      // tokenExpiration : null,
    });
  },

  autoLogout(context) {
    context.dispatch('logout');
    context.commit('setAutoLogout');
  }
}
```

```
다음으로 ms를 더하여 구한 값[expirationDate]을 이용하여 setTimeout을 이용하여 만료시간이 되면 logout을 하도록 logic을 작성한다.[1]
logout을 시도하게 되면 localStorage에서 만료토큰을 삭제하고, login했을 때 설정한 setTimeout을 clear해준다. timer는 어디에서든 사용할 수 있도록
전역으로 export하는 모듈 바깥에 선언한다.[2]
tryLogin[autologin] 에서도 localStorage에서 값을 이용하여 setTimeout을 설정해준다. 여기서는 로그인을 한 후 시간이 많이 지났기 때문에
[ 저장된 만료토큰 - 현재시간 ] 을 계산하여 계산한 값이 10초 이내라면 return하여 login을 하지 않는다. 대신 10초보다 많이 남았다면
setTimeout으로 logout을 설정하고 login을 시도한다.
```

```
logout을 진행하여도 권한이 제한된 화면에서 로그아웃이 되어버려도 그 화면에 머물러있게 되어버린다. [ logout시 redirect 하지 않기 때문에]
이 것을 처리하기 위해 logout을 하게되면 값을 비교하여 coach화면이나 auth화면으로 redirect 시킨다. 먼저 logout이 되었는지에 대한 상태값을
저장하기 위해 state에 didLogout이라는 값을 추가하고, mutations, getters 도 추가해준다. [1]
```

```js
// auth/index.js

import mutations from './mutations'
import actions from './actions'
import getters from './getters'
export default {
  namespaced: true,
  state() {
    return {
      userId: null,
      token: null,
      tokenExpiration: null,
      didAutoLogout: false,           // [1]
    }; 
  },
  mutations,
  getters,
  actions,
}
```

```js
// auth/mutations.js

export default {
  setUser(state, payload) {
    state.token = payload.token;
    state.userId = payload.userId;
    state.didAutoLogout = false;
    // state.tokenExpiration = payload.tokenExpiration;
  },
  setAutoLogout(state) {
    state.didAutoLogout = true;              // [1]
  }
};

```


```js
// auth/getters.js

export default {
  userId(state) {
    return state.userId;
  },
  token(state) {
    return state.token;
  },
  isAuthenticated(state) {
    return !!state.token;
  },
  didAutoLogout(state) {
    return state.didAutoLogout;            //[1]
  }
}
```


```js
// auth/actions.js

let timer;

export default {
  async login(context, payload) {
    return context.dispatch('auth', {
      ...payload,
      mode: 'login',
    })
  },
  async signup(context, payload) {
    return context.dispatch('auth', {
      ...payload,
      mode: 'signup',
    })
  },

  async auth(context, payload) {
    const mode = payload.mode;
    let url = `https://identitytoolkit.googleapis.com/v1/accounts:signInWithPassword?key=AIzaSyAJLkNz9pbIzgj89yJwk3SXJW3MUuTp5L8`
    if (mode === 'signup') {
      url = `https://identitytoolkit.googleapis.com/v1/accounts:signUp?key=AIzaSyAJLkNz9pbIzgj89yJwk3SXJW3MUuTp5L8`
    }
    const response = await fetch(url, {
      method: 'POST',
      body: JSON.stringify({
        email: payload.email,
        password: payload.password,
        returnSecureToken: true,
      })
    });

    const responseData = await response.json();

    if (!response.ok) {
      const error = new Error(responseData.message || 'Failed to authenticate. check your login data');
      throw error;
    }

    // const expiresIn = +responseData.expiresIn * 1000;
    const expiresIn = 5000; // test
    const expirationDate = new Date().getTime() + expiresIn;

    localStorage.setItem('token', responseData.idToken);
    localStorage.setItem('userId', responseData.localId);
    localStorage.setItem('tokenExpiration', expirationDate);

    timer = setTimeout(() => {
      context.dispatch('autoLogout');                                      //[1]
    }, expiresIn);

    context.commit('setUser', {
      token: responseData.idToken,
      userid: responseData.localId,
      // tokenExpiration: expirationDate,
    });
  },

  tryLogin(context) {
    const token = localStorage.getItem('token');
    const userId = localStorage.getItem('userId');
    const tokenExpiration = localStorage.getItem('tokenExpiration');

    const expiresIn = +tokenExpiration - new Date().getTime();

    // if (expiresIn <= 10000) {
    if (expiresIn <= 0) {
      return;
    }

    setTimeout(() => {
      context.dispatch('autoLogout');                                           //[1]
    }, expiresIn)

    if (token && userId) {
      context.commit('setUser', {
        token: token,
        userId: userId,
        // tokenExpiration: null,
      })
    }

  },

  logout(context) {
    localStorage.removeItem('token');
    localStorage.removeItem('userId');
    localStorage.removeItem('tokenExpiration');

    clearTimeout(timer);

    context.commit('setUser', {
      token : null,
      userId : null,
      // tokenExpiration : null,
    });
  },

  autoLogout(context) {                                                        //[1]
    context.dispatch('logout');                                              //[1]
    context.commit('setAutoLogout');                                          //[1]
  }
}
```

```
이제  setTimeout에서 logout 했던 로직을 autoLogout actions를 추가하면서 logout -> autoLogout으로 변경한다. [1]
autoLogout을 하게되면 기존 logout logic과 setAutoLogout/mutations 을 통해 didLogout 값을 true로 변경한다.[상태변경]
```

```vue
//App.vue

<template>
  <the-header/>
  <router-view v-slot='slotProps'>
    <transition name='route' mode='out-in'>
      <component :is='slotProps.Component'></component>
    </transition>
  </router-view>
</template>

<script>
import TheHeader from '@/components/layout/TheHeader';

export default {
  name: 'App',
  components: {
    TheHeader
  },
  created() {
    this.$store.dispatch('auth/tryLogin');
  },
  computed: {                                                      //[1]
    didAutoLogout() {                                              // [1]
      return this.$store.getters['auth/didAutoLogout'];            // [1]
    }
  },

  watch: {
    didAutoLogout(newValue, oldValue) {                              //[1]
      console.log({newValue, oldValue});                           //[1]
      if (newValue && newValue !== oldValue) {                    //[1]
        this.$router.replace('/coaches');                         //[1]
      }
    },
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap");

* {
  box-sizing: border-box;
}

html {
  font-family: "Roboto", sans-serif;
}

body {
  margin: 0;
}

.route-enter-from {
  opacity: 0;
  transform: translateY(-30px);
}

.route-leave-to {
  opacity: 0;
  transform: translateY(30px);
}

.route-enter-active{
  transition: all 0.3s ease-out;
}
.route-leave-active{
  transition: all 0.3s ease-in;
}

.route-enter-to,
.route-leave-from {
  opacity: 1;
  transform: translateY(0);
}
</style>
```

```
다음으로 App.vue [모든 페이지의 base가 되는](모든 페이지는 app.vue 내에서 렌더링된다) 에서 didLogout값을 watch 하면서 
값의 변경이 일어나면 autoLogout한것으로 간주하여 페이지를 /coach 페이지로 redirect 시킨다. [1]
새로 전달된 값 [autoLogout 되었는가에 대한 값] 이true 이고, 이전 값 과 새롭게 전달된 값이 같지 않다면 [ 로그인 -> 로그아웃] 
해당 로직을 실행한다.
```

```js
//auth/mutaitons.js

export default {
  setUser(state, payload) {
    state.token = payload.token;
    state.userId = payload.userId;
    state.didAutoLogout = false;                               //
    // state.tokenExpiration = payload.tokenExpiration;
  },
  setAutoLogout(state) {
    state.didAutoLogout = true;
  }
};
```

```
마지막으로 login을 했을 때는 didAutoLogout값을 false로 변경해준다.
```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880058#content
