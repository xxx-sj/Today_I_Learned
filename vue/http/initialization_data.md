# 데이터 초기화

```
화면이 처음 로딩될 때 데이터베이스의 데이터를 초기화 해야할 때가 있다.
life cycle에서 mounted에서 데이터를 가져오는 메소드를 호출하면 된다. 
```

```vue
<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click='loadExperiences'>Load Submitted Experiences</base-button>
      </div>
      <p v-if='isLoading'>Loading...</p>
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

    }
  },
  methods: {
    loadExperiences() {
      this.isLoading = true;
      fetch('https://vue-http-demo-2273e-default-rtdb.firebaseio.com/surveys.json')
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
인터넷 속도에 의해 데이터를 늦게 가져와 지거나, 혹은 데이터베이스의 select 속도가 늦어져 데이터를 
초기화하는데 오래 걸릴 수 있다. 유저는 이러한 상황을 모르기 때문에 UX차원에서 Loading이라는 spinner를 추가해주면 좋다.
[ 여기서는 text만 작성하였다. ]
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
