# output and incoming requests

```
requests를 받아 coach가 확인할 수 있는 페이지를 작성한다.
```

```vue
//RequestReceived.vue


<template>
  <section>
    <base-card>
      <header>
        <h2>Requests Received</h2>
      </header>
      <ul v-if='hasRequests'>
        <request-item
          v-for='request in receivedRequests'
          :key='request.id'
          :email='request.email'
          :message='request.message' />
      </ul>
      <h3 v-else>You haven't received any requests yet!</h3>
    </base-card>
  </section>
</template>

<script>
import RequestItem from '@/components/requests/RequestItem';
export default {
  name: 'RequestsReceived',
  components: { RequestItem },
  computed: {
    receivedRequests() {
      return this.$store.getters['requests/requests'];
    },
    hasRequests() {
      return this.$store.getters['requests/hasRequests'];
    }
  }
};
</script>

<style scoped>
header {
  text-align: center;
}

ul {
  list-style: none;
  margin: 2rem auto;
  padding: 0;
  max-width: 30rem;
}

h3 {
  text-align: center;
}
</style>
```

```
reuqest에 값이 있다면 ul로 list를 보여주고 아니라면 h3 태그를 보여준다.
requests는 store를 통해 가져오게 되며, valide check는 getters에서 처리한다.
```

```vue
//RequestItem.vue

<template>
  <li>
    <div>
      <a :href='emailLink'>{{ email }}</a>
    </div>
    <p>{{  message }}</p>
  </li>

</template>

<script>
export default {
  props: ['email', 'message'],
  computed: {
    emailLink() {
      return 'mailto:' + this.email;
    }
  },
  name: 'RequestItem',
};
</script>

<style scoped>
li {
  margin: 1rem 0;
  border: 1px solid #ccc;
  padding: 1rem;
}

a {
  color: #3d008d;
  text-decoration: none;
  font-weight: bold;
}

a:hover,
a:active {
  color: #8d007a;
}

p {
  margin: 0.5rem 0 0 0;
}
</style>
```

```
request로 온 하나의 item을 나타내는 component이다. 간단하게 message, a 태그로 email을 나타낸다.
다음은 requests의 store code이다.
```

```js
//index.js

import mutations from '@/store/modules/requests/mutations';
import actions from '@/store/modules/requests/actions';
import getters from '@/store/modules/requests/getters';

export default {
  namespaced: true,
  state() {
    return {
      requests: [],
    };
  },
  getters,
  mutations:mutations,
  actions: actions,
}
```

```js
//getters.js

export default {
  requests(state) {
    return state.requests;
  },
  hasRequests(state) {
    return state.requests && state.requests.length > 0
  }
};
```

```js
//mutations.js

export default {
  addRequest(state, payload) {
    state.requests.push(payload);
  }
};
```

```js
//actions.js

export default {
  contactCoach(context, payload) {
    const newRequest = {
      id: new Date().toISOString(),
      coachId: payload.coachId,
      email: payload.email,
      message: payload.message,
    };
    context.commit('addRequest', newRequest);
  }
};
```



![image](https://user-images.githubusercontent.com/62305110/210140328-d3b97f20-3138-4ca3-b656-13c7c6131ba2.png)
![image](https://user-images.githubusercontent.com/62305110/210140332-31ca1712-81f3-4c68-864e-5458e2c7ed52.png)


##### 출처 : https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21879962#content
