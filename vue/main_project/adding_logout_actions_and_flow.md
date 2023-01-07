# adding logout actions and flow


```
logout 작업을 진행한다. login과는 다르게 logout에서는 따로 전달해야하는
payload가 없다. 
login과는 반대로 state가 저장되어있는 token, id, expiretoken등 을 null로 변경해주면 된다/
```

```js
// auth/actions.js

export default {
  async login(context, payload) {
    const response = await fetch(`https://identitytoolkit.googleapis.com/v1/accounts:signInWithPassword?key=AIzaSyAJLkNz9pbIzgj89yJwk3SXJW3MUuTp5L8`, {
      method: 'POST',
      body: JSON.stringify({
        email: payload.email,
        password: payload.password,
        returnSecureToken: true,
      })
    });

    const responseData = await response.json();

    if (!response.ok) {
      console.log({responseData});
      const error = new Error(responseData.message || 'Failed to authenticate. check your login data');
      throw error;
    }

    console.log({responseData});
    context.commit('setUser', {
      token: responseData.idToken,
      userid: responseData.localId,
      tokenExpiration: responseData.expiresIn,
    })
  },
  async signup(context, payload) {
    const response = await fetch(`https://identitytoolkit.googleapis.com/v1/accounts:signUp?key=AIzaSyAJLkNz9pbIzgj89yJwk3SXJW3MUuTp5L8`, {
      method: 'POST',
      body: JSON.stringify({
        email: payload.email,
        password: payload.password,
        returnSecureToken: true,
      })
    });

    const responseData = await response.json();

    if (!response.ok) {
      console.log({responseData});
      const error = new Error(responseData.message || 'Failed to authenticate. check your login data');
      throw error;
    }

    console.log({responseData});
    context.commit('setUser', {
      token: responseData.idToken,
      userid: responseData.localId,
      tokenExpiration: responseData.expiresIn,
    })
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

```js
/auth/mutations.js

export default {
  setUser(state, payload) {
    state.token = payload.token;
    state.userId = payload.userId;
    state.tokenExpiration = payload.tokenExpiration;
  },
};
```

```vue
//TheHeader.vue

<template>
  <header>
    <nav>
      <h1><router-link to='/'>Find a Coach</router-link></h1>
      <ul>
        <li><router-link to='/coaches'>All Coaches</router-link></li>
        <li v-if='isLoggedIn'><router-link to='/requests'>Requests</router-link></li>
        <li v-else>
          <router-link to='/auth'>Login</router-link>
        </li>
        <li v-if='isLoggedIn'>
          <base-button @click='logout'>Logout</base-button>   //
        </li>
      </ul>
    </nav>
  </header>
</template>

<script>
export default {
  name: 'TheHeader',
  computed: {
    isLoggedIn() {
      return this.$store.getters['auth/isAuthenticated'];
    }
  },
  methods: {
    logout() {
      this.$store.dispatch('auth/logout');
    }
  }
};
</script>

<style scoped>
header {
  width: 100%;
  height: 5rem;
  background-color: #3d008d;
  display: flex;
  justify-content: center;
  align-items: center;
}

header a {
  text-decoration: none;
  color: #f391e3;
  display: inline-block;
  padding: 0.75rem 1.5rem;
  border: 1px solid transparent;
}

a:active,
a:hover,
a.router-link-active {
  border: 1px solid #f391e3;
}

h1 {
  margin: 0;
}

h1 a {
  color: white;
  margin: 0;
}

h1 a:hover,
h1 a:active,
h1 a.router-link-active {
  border-color: transparent;
}

header nav {
  width: 90%;
  margin: auto;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

header ul {
  list-style: none;
  margin: 0;
  padding: 0;
  display: flex;
  justify-content: center;
  align-items: center;
}

li {
  margin: 0 0.5rem;
}
</style>
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880042#content
