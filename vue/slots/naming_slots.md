# 네이밍된 slots [ multiple_slots ]

```
slot을 여러가지를 정의하여 사용할 수 있다. 한 component 내에 여러  slot을 정의하게 되면 해당 slot에 
name attribute를 추가해줘야 하는데, 만약 추가 하지않은 slot이 있다면 해당 슬롯은 component의 default slot이 된다.
단, component 내 default slot은 하나여야 한다 [ name이 정의되지않은 슬롯은 하나여야 한다. ]
```

```vue
//baseCard.vue

<template>
  <div>
    <header>
      <slot name="header"></slot>
    </header>
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
slot을 적용할 때는 slot에 들어가야할 부분을 [ 아래에서는 header에 해당한다. ] template로 감싸고 v-slot attribute를 추가해준다.
[ <template v-slot:header> ] v-slot 지시문은 template가 감싼 content가 어디에 위치하여야 하는지 vue에게 알려주는 것이다.
template 태그는 화면에 렌더링되지 않는다. template에 v-slot으로 정의하여 감싸지 않은 content는 자동으로 default slot으로 이동한다. [ <p> ]
userInfo에 정의한 style은 base-card로 감싼 tag에 적용되지 않기 때문에 style은 base-card로 옮긴다.
```

```vue
//baseCard.vue

<template>
  <section>
    <base-card>
      <header> <template v-slot:header>
        <h3>{{ fullName }}</h3>
        <base-badge :type="role" :caption="role.toUpperCase()"></base-badge>
      </header> </template>
      <p>{{ infoText }}</p> //default slot 
    </base-card>
  </section>
</template>

<script>
export default {
  props: ['fullName', 'infoText', 'role'],
};
</script>

<style scoped>
</style>
```

### slot 적용 후
```vue
//baseCard.vue

<template>
  <section>
    <base-card>
      <template v-slot:header>
        <h3>{{ fullName }}</h3>
        <base-badge :type="role" :caption="role.toUpperCase()"></base-badge>
      </template>
      <template v-slot:default> //선택적
      <p>{{ infoText }}</p>
      </template>
    </base-card>
  </section>
</template>

<script>
export default {
  props: ['fullName', 'infoText', 'role'],
};
</script>

<style scoped>
</style>
```

```
적용된 내용은 아래와 같다.
```

```vue
//userInfo.vue

<template>
  <section>
    <div>
      <header>
        <h3>{{ fullName }}</h3>
        <base-badge :type="role" :caption="role.toUpperCase()"></base-badge>
      </header>
      <p>{{ infoText }}</p>
    </div>
  </section>
</template>

<script>
export default {
  props: ['fullName', 'infoText', 'role'],
};
</script>

<style scoped>
div {
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
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide
