# adding auto login

```
현재 reload를 하면 login이 풀리는 문제가 있다. 이유는 vuex에서만 값을 저장하기 때문에 
reload를 하게되면 vue app이 다시 실행되게 되어 저장했던 값이 없어지기 때문이다.
이 문제를 해결하기 위해 login을 할 때 브라우저의 built-in api인 localStorage에 값을 저장하고
app이 reload 되었을 때 localStroage에 값이 있다면 auto login을 실행하게 하는 것이다.
```

```js
//auth/actions.js

export default {
  async login(context, payload) {
    return context.dispatch('auth', {         //
      ...payload,                            //
      mode: 'login',                       //
    })
  },
  async signup(context, payload) {
    return context.dispatch('auth', {              //
      ...payload,                               //
      mode: 'signup',                            //
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
      tokenExpiration: responseData.expiresIn,
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
    context.commit('setUser', {
      token : null,
      userId : null,
      tokenExpiration : null,
    });

  },
}
```

```
기존의 중복코드인 login과 signup request 부분을 auth method로 빼고, auth method 내에서
로그인 성공 후 값을 setUser를 통해 저장할 때 localStorage에도 저장을하게 된다.
이제 페이지를 reload하게 될 때  login을 시키면 되는데, 해당 로직을 어디에서 실행하면 좋을지 생각하게
된다면 모든 페이지의 parent인 App.vue에서 created 할 때 해당 logic을 실행하는것이 가장 이상적이다.
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
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880056#content
