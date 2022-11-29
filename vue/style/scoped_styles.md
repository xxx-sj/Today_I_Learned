# 스타일의 범위

[2022-11-29]

```
스타일 태그를 사용해서 css 작성 시 어디에서 작성을 하던 글로벌하게 적용이 된다.
```

![image](https://user-images.githubusercontent.com/62305110/204540065-b127f74d-6f2f-4ef7-a966-15e9cc21625a.png)

```vue
//BadgeList.vue

<template>
  <section>
    <h2>Available Badges</h2>
    <ul>
      <li>
        <base-badge type="admin" caption="ADMIN"></base-badge>
      </li>
      <li>
        <base-badge type="author" caption="AUTHOR"></base-badge>
      </li>
    </ul>
  </section>
</template>

<style>
section h2 {
  margin: 0.5rem 0;
  color: #3a3a3a;
}
ul {
  list-style: none;
  margin: 0;
  padding: 0;
  display: flex;
  flex-direction: row;
}

li {
  margin-right: 1rem;
}
</style>
```

```
글로벌한 것이 의도한 것이라면 괜찮겠지만, 다른 component에 영향을 끼치는건 좋지 않을뿐더러 생각한 대로
css가 되지 않을 수 있다. 해당 파일에서만 style이 적용되게 하고 싶다면, style 태그에 scoped 키워드를 추가한다.
```

```vue
//BadgeList.vue

<template>
  <section>
    <h2>Available Badges</h2>
    <ul>
      <li>
        <base-badge type="admin" caption="ADMIN"></base-badge>
      </li>
      <li>
        <base-badge type="author" caption="AUTHOR"></base-badge>
      </li>
    </ul>
  </section>
</template>

<style scoped>
section h2 {
  margin: 0.5rem 0;
  color: #3a3a3a;
}
ul {
  list-style: none;
  margin: 0;
  padding: 0;
  display: flex;
  flex-direction: row;
}

li {
  margin-right: 1rem;
}
</style>
```

```
vue가 실제로 구현한 방법이 궁금하다면, 개발자 도구를 열어서 확인해보면, 요소에 특별한 attributes를[data-v-927321a] 부여하여 적용한다.
[ header[data-v-927321a] ]
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide
