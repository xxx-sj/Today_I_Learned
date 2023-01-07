# attaching token to outgoing requests

```
request를 보낼 때 token을 통해 제한된 api를 사용할 수 있게 만들었기 때문에 특정 api에 대해 
login을 통해 가져온 token을 request에 담아서 보낸다. 먼저 login할 때 mutations을 통해 state에 token을 저장하기 때문에
해당 getters를 auth 에 만든다.
```

```js
//auth/getters.js

export default {
  userId(state) {
    return state.userId;
  },
  token(state) {
    return state.token;
  }
}
```

```
이제 각각의 api에 fire-base doc에도 나와있듯이 queryString 으로 key는 auth로 value는 token을 전달한다.
```

```js
// coaches/actions.js

export default {
  async registerCoach(context, data) {
    const userId = context.rootGetters.userId;
    const coachData = {
      firstName: data.first,
      lastName: data.last,
      description: data.desc,
      hourlyRate: data.rate,
      areas: data.areas,
    };

    const token = context.rootGetters['auth/token'];           //

    const response = await fetch(`https://vue-http-demo-2273e-default-rtdb.firebaseio.com/coaches/${userId}.json?auth=${token}`, {  //
      method: 'PUT',
      body: JSON.stringify(coachData),
    });

    // const resonseData = await response.json();

    if(!response.ok) {
      // error ...
    }

    context.commit('registerCoach', {
      ...coachData,
      id: userId,
    });
  },

  async loadCoaches(context, payload) {
    if (!payload.forceRefresh && !context.getters.shouldUpdate) {
      return;
    }

    const response = await fetch(`https://vue-http-demo-2273e-default-rtdb.firebaseio.com/coaches.json`)
    const responseData = await response.json();

    if (!response.ok) {
      const error = new Error(responseData.message || 'failed to fetch');
      throw error;
    }

    const coaches = [];

    for (const key in responseData) {
      const coach = {
        id: key,
        firstName: responseData[key].firstName,
        lastName: responseData[key].lastName,
        description: responseData[key].description,
        hourlyRate: responseData[key].hourlyRate,
        areas: responseData[key].areas,
      };

      coaches.push(coach);
    }

    context.commit('setCoaches', coaches);
    context.commit('setFetchTimestamp');
  }
};
```

```js
// requests/actions.js


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
    const token = context.rootGetters['auth/token'];             //
    const response = await fetch(`https://vue-http-demo-2273e-default-rtdb.firebaseio.com/requests/${coachId}.json?auth=${token}`);
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
token은 auth에 존재하는 state값이기에 해당 store의 getter가 아닌 rootGetters를 통해 접근해야 한다는 것을 명심하자.
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880032#content
