# slots
```
slots 은 template에 정의할 때 v-slot:[name] 또는 #[name] 으로 정의할 수 있다.
```

```
2가지의 component가 공통적으로 section 태그를 갖고 있으며, 같은 style을 가지고 있다.
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
section {
  margin: 2rem auto;
  max-width: 30rem;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  padding: 1rem;
}

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

```vue
//userInfo.vue

<template>
  <section>
    <header>
      <h3>{{ fullName }}</h3>
      <base-badge :type="role" :caption="role.toUpperCase()"></base-badge>
    </header>
    <p>{{ infoText }}</p>
  </section>
</template>

<script>
export default {
  props: ['fullName', 'infoText', 'role'],
};
</script>

<style scoped>
section {
  margin: 2rem auto;
  max-width: 30rem;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  padding: 1rem;
}

section header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>
```

```
이런 경우 전역적으로 section의 스타일을 정의해서 사용할 수도 있지만, 독립형 구성요소를 생성하기 위해 하나의 
component를 생성하고, 그 안에서 slot을 통해 동적으로 content를 변경한다.
```

```vue
//BaseCard.vue

<template>
  <div>
    <slot></slot>
  </div>
</template>

<script>
export default {
  name: "BaseCard"
}
</script>

<style scoped>
div {
  margin: 2rem auto;
  max-width: 30rem;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  padding: 1rem;
}
</style>
```

```
이렇게 생성한 component를 main.js에 등록하고, 필요한 component에서 사용한다.
```

```vue
//userInfo.uve

<template>
  <section>
    <base-card>
      <header>
        <h3>{{ fullName }}</h3>
        <base-badge :type="role" :caption="role.toUpperCase()"></base-badge>
      </header>
      <p>{{ infoText }}</p>
    </base-card>
  </section>
</template>

<script>
export default {
  props: ['fullName', 'infoText', 'role'],
};
</script>

<style scoped>
section {
  margin: 2rem auto;
  max-width: 30rem;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  padding: 1rem;
}

section header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>
```

```
마치 content를 감싸는 래퍼로써 사용하게 되며, base-card 태그 사이에 정의한 content들은 해당 base-card에 정의한 slot 부분에
들어가게 된다. 붙여서 보면 아래와 같다.
```

```vue
//userInfo.vue

<template>
  <section>
    <div> //<base-card>
    //slot 시작부분
      <header>
        <h3>{{ fullName }}</h3>
        <base-badge :type="role" :caption="role.toUpperCase()"></base-badge>
      </header>
      <p>{{ infoText }}</p>
      //slot 끝 부분
    </div> // </base-card>
  </section>
</template>

<script>
export default {
  props: ['fullName', 'infoText', 'role'],
};
</script>

<style scoped>

section header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

//bass-card의 style부분
div {
  margin: 2rem auto;
  max-width: 30rem;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  padding: 1rem;
}
</style>
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide
