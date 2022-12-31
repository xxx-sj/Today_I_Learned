# sending http requests to store coach data


```
register를 firebase와 연동하기
```

```js
//Coaches/actions.js

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

    const response = await fetch(`https://vue-http-demo-2273e-default-rtdb.firebaseio.com/coaches/${userId}.json`, {
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
  }
};
```

```
register component에서 등록할 때 전달받은 데이터를 fetch를 통해 firebase에 저장한다.
저장할때는 userId를 기준으로 put하며, context.commit할 때 coachData를 객체복사와 id를 추가한다.
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21879970#content
