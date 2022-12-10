# 에러 처리

```
데이터베이스에 데이터가 없거나, 서버의 요청 시간초과, 사이트 오류등이 발생 할 수 있다.

데이터베이스에 데이터가 없는경우는 for문을 돌 수 없기 때문에 렌더링하는 태그 부분에서
results에 대한 체크를 해주면 된다. [ !isLoading[로딩되었는가?] && results[ 데이터초기화가 되었는가?] && results.length > 0 [데이터가 존재하는가? ]]
results의 데이터가 없을 경우 조건은 초기화되지않거나, 데이터 length가 0일 경우이다.
```

```html
<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click='loadExperiences'>Load Submitted Experiences</base-button>
      </div>
      <p v-if='isLoading'>Loading...</p>
      <p v-else-if='!isLoading && (!results || results.length == 0)'>No stored experiences found. Start adding some survey results first.</p>
      <ul v-else-if='!isLoading && results && results.length > 0'>
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
    </base-card>
  </section>
</template>
```


### tech error handling
```
fetch method에서 catch callback 메소드에서 처리를 하는데 catch facllback은 then block이나, fetch request에서 발생한 erorr를 
처리할 수 있다.
```

```vue
//userExperiences.vue

<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click='loadExperiences'>Load Submitted Experiences</base-button>
      </div>
      <p v-if='isLoading'>Loading...</p>
      <p v-else-if='!isLoading && error'>
        {{ error }}
      </p>
      <p v-else-if='!isLoading && (!results || results.length == 0)'>No stored experiences found. Start adding some survey results first.</p>
      <ul v-else>
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from './SurveyResult.vue';

export default {
  components: {
    SurveyResult,
  },

  data() {
    return {
      results: [],
      isLoading: false,
      error: null,
    }
  },
  methods: {
    loadExperiences() {
      this.isLoading = true;
      this.error = null;
      fetch('https://vue-http-demo-2273e-default-rtdb.firebaseio.com/surveys')
        .then((response) => {
        if (response.ok) {
          return response.json();
        }
      }).then((data) => {
        this.isLoading = false;
        const results = [];
        for(const id in data) {
          results.push({id: id, name: data[id].name, rating: data[id].rating});
        }
        this.results = results;
      }).catch((error) => {
        console.log(error);
        this.isLoading = false;
        this.error = "failed to fetch data - please try again later";
      });
    },
  },

  mounted() {
    this.loadExperiences();
  },
};
</script>
```

```
error가 발생하면 then절을 타지 않기 때문에 잊지않고 isLoading을 false로 초기화한다. 또한 매번 요청 시에
error를 null로 초기화해야 이전에 요청에 대한 error data가 남아있지 않다.
```

```
다른 서버에서 던지는 error [서버 사이드 에러] status 400 or 500 종류의 에러는 아래와 같이 처리한다.
```

```
fetch('https://vue-http-demo-2273e-default-rtdb.firebaseio.com/surveys.json', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({ name: this.enteredName, rating: this.chosenRating }),
  }).then((response) => {
    if (response.ok) {
      console.log(response);
    } else {
      throw new Error('Could not save data!');
    }
  }).catch((error)=> {
    console.log(error);
    this.error = error.message;
  });

  this.enteredName = '';
  this.chosenRating = null;
},
```

```
then block을 실행하게 될텐데 ok일 경우는 성공 메시지를 보여주고 아닐경우에 error의 종류에 따라서
Error object를 생성하여 처리한다.
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
