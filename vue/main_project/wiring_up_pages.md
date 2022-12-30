# wiring up pages

```
COACH 페이지와 CONTACT 페이지 연결하기
```

```vue
//COachesList.vue

<template>
  <section>
    FILTER
  </section>
  <section>
    <div class='controls'>
      <button>Refresh</button>
      <router-link to='/register'>Register as Coach</router-link>
    </div>
    <ul>LIST OF COACHES</ul>
  </section>
</template>

<script>
export default {
  name: 'CoachesList'
};
</script>
```

```
coach 상세 페이지 /coach/c1
```

```vue
//CoachDetail.vue

<template>
  <div>
    DETAILS FOR COACH
    <router-view></router-view>
    <router-link to='/coaches/c1/contact'>Contact</router-link>
  </div>
</template>

<script>
export default {
  name: 'CoachDetail'
};
</script>
```

```
상세 페이지 내부에서 contact 페이지로 이동 -> /coaches/c1/contact
contact route는 /coaches/:id 의 child route 이기때문에 CoachDetail 내부에서 router-view를 통해 보여주게된다.
```

```
주의!!
router child 작성 시 맨 앞 / 를 빼야 한다. /를 빼지않으면 절대적 경로로써 데이터가 표현되지 않는다..?
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
