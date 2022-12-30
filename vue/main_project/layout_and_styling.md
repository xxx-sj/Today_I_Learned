# layout and styling

```
첫 화면은 coachesList가 보여지는 화면이다.
```

```vue
//CoahesList.vue

<template>
  <section>
    FILTER
  </section>
  <section>
    LIST OF COACHES
  </section>
</template>

<script>
export default {
  name: 'CoachesList'
};
</script>

<style scoped>

</style>
```

```
공통 레이아웃으로 사용할 TheHeader 파일을 App.vue에 local component로 등록한다.
```

```vue
//TheHeader.vue

<template>
  <header>
    <nav>
      <h1><router-link to='/'>Find a Coach</router-link></h1>
      <ul>
        <li><router-link to='/coaches'>All Coaches</router-link></li>
        <li><router-link to='/requests'>Requests</router-link></li>
      </ul>
    </nav>
  </header>
</template>

<script>
export default {
  name: 'TheHeader'
};
</script>

<style scoped>
header {
  width: 100%;
  height: 5rem;
  background-color: #3d008d;
  display: flex;
  justify-content: center;
  align-items: center;
}

header a {
  text-decoration: none;
  color: #f391e3;
  display: inline-block;
  padding: 0.75rem 1.5rem;
  border: 1px solid transparent;
}

a:active,
a:hover,
a.router-link-active {
  border: 1px solid #f391e3;
}

h1 {
  margin: 0;
}

h1 a {
  color: white;
  margin: 0;
}

h1 a:hover,
h1 a:active,
h1 a.router-link-active {
  border-color: transparent;
}

header nav {
  width: 90%;
  margin: auto;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

header ul {
  list-style: none;
  margin: 0;
  padding: 0;
  display: flex;
  justify-content: center;
  align-items: center;
}

li {
  margin: 0 0.5rem;
}
</style>
```
![image](https://user-images.githubusercontent.com/62305110/210090608-79b669af-481d-4b3e-905e-ddc8b0383506.png)

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
