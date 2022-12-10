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
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
