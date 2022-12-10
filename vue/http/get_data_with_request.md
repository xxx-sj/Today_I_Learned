# 데이터베이스에서 데이터 가져오기

```js
<script>
import SurveyResult from './SurveyResult.vue';

export default {
  components: {
    SurveyResult,
  },

  data() {
    return {
      results: [],
    }
  },
  methods: {
    loadExperiences() {
      fetch('https://vue-http-demo-2273e-default-rtdb.firebaseio.com/surveys.json')
        .then((response) => {
        if (response.ok) {
          return response.json();
        }
      }).then((data) => {
        const results = [];
        for(const id in data) {
          results.push({id: id, name: data[id].name, rating: data[id].rating});
        }
        this.results = results;
      });
    },
  }
};
</script>
```

```
firebase는 기본적으로 json 데이터를 주고받는다. fetch는 promise를 반환하는데, callback method then을 통해
처음 then 블록에서는 response의 http status로 데이터를 구별하고, 두 번째 then에서는 표현할 데이터의 구조대로 변경하여
this.$data.results를 초기화한다.
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
