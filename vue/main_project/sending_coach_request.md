# sending coaching requests 

```
coach에게 requests를 보내는 부분을 firebase와 연동하기
```

```js
//store/modules/requests/actions.js


export default {
  async contactCoach(context, payload) {
    const newRequest = {
      email: payload.email,
      message: payload.message,
    };
    const response = await fetch(`https://vue-http-demo-2273e-default-rtdb.firebaseio.com/requests/${payload.coachId}.json`,{
      method: 'POST',
      body: JSON.stringify(newRequest),
    })

    const responseData = await response.json();

    if(!response.ok) {
      const error = new Error(responseData.message || 'Failed to send request');
      throw error;
    }

    newRequest.id = responseData.name;
    newRequest.cocahId = payload.coachId;

    context.commit('addRequest', newRequest);
  },
  async fetchRequests(context) {
    const coachId = context.rootGetters.userId;
    const response = await fetch(`https://vue-http-demo-2273e-default-rtdb.firebaseio.com/requests/${coachId}.json`);
    const responseData = await response.json();

    if(!response.ok) {
      const error = new Error(responseData.message || 'Failed to fetch request');
      throw error;
    }

    const requests = [];

    console.log(responseData);

    for(const key in responseData) {
      const request = {
        id: key,
        coachId: coachId,
        email: responseData[key].email,
        message: responseData[key].message,
      };
      requests.push(request);
    }

    context.commit('setRequests', requests);
  }
};
```

```
POST로 데이터를 보낼 때 email와 message만 보낸다  firebase내에서 name으로 고유한 id를 부여하며, POST값의 return으로
해당 값을 받는데, store에 저장할 때 id값과 caochId를 추가하여 저장한다. coachId를 보내지 않는 것은 해당 coachId정보를 갖고
조회를 하기 때문에, 따로 넣지않고 push할 때 같이 넣어준다.
```

```
get해서 requests의 list를 가져올 때는 main store에 있는 현재는 하드코딩되어있는 id를 가지고 조회를 한다.
조회 후 application에 맞는 형식으로 변환하여 값을 저장한다.
```


```js
//request/getters.js

export default {
  requests(state, _, _2, rootGetters) {
    const coachId = rootGetters.userId;
    return state.requests.filter(req => req.coachId === coachId);
  },
  hasRequests(_, getters) {
    return getters.requests && getters.requests.length > 0
  }
};
```

```js
//requests/mutations.js

export default {
  addRequest(state, payload) {
    state.requests.push(payload);
  },
  setRequests(state, payload) {
    state.requests = payload;
  }
};
```

```vue
//RequestsReceived.vue

<template>
  <base-dialog :show='!!error' title='An error occurred!' @close='handleError'>
    <p>{{ error }}</p>
  </base-dialog>
  <section>
    <base-card>
      <header>
        <h2>Requests Received</h2>
      </header>
      <base-spinner v-if='isLoading' />
      <ul v-else-if='hasRequests && !isLoading'>
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
  data() {
    return {
      isLoading: false,
      error: null,
    }
  },
  created() {
    this.loadRequests();
  },
  computed: {
    receivedRequests() {
      return this.$store.getters['requests/requests'];
    },
    hasRequests() {
      return this.$store.getters['requests/hasRequests'];
    }
  },
  methods: {
    async loadRequests() {
      this.isLoading = true;
      try {
        await this.$store.dispatch('requests/fetchRequests');
      } catch(error) {
        this.error = error.message || 'Something is Failed';
      }
      this.isLoading = false;
    },
    handleError() {
      this.error = null;
    },
  },
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

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21879986#content
