# filtering requests active coach

```
해당 coach에 대한 request만을 보여줄 수 있게 store reuqests를 수정한다.
```

```js
// store/requests/getters.js


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

```
rooterGetters를 통해 main store에서 coach id를 가져와 filtering 한다.
hasRequest도 이전 state의 requests에서 getters의 filtering된 request를 받는다.
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21879968#content
