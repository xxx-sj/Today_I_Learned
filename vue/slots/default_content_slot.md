# 기본 슬롯 컨텐츠

```
슬롯 태그 사이에 기본적으로 정의할 content를 정의할 수 있다. 이렇게 정의한 컨텐츠들은
다른 컴포넌트에서 사용할 때 해당 slot을 사용하지 않는다면 기본으로 정의한 컨텐츠가 보이게 된다.
```

```vue
//base-card.vue

<template>
  <div>
    <header>
      <slot name="header">
        <h2>Thd Default</h2>
      </slot>
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

section header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>
```

```
default content를 보여주고 싶지 않을 때도 있을 것이다. slot태그 사이에 정의하게 되면 사용하지 않으면 렌더링되어 버린다.
vue 에서는 $slot이라는 property를 제공하는데 해당 프로퍼티는 현재 slot을 정의한 component를 사용하는 component에서 
특정 slot을 사용하는지 하고 있지 않은지를 판단하게 해준다. 예를 들어, A라는 component에서 header라는 component를 사용하지 않으면,
slot을 정의한 component에서 this.$slots.[name]으로 확인해보면 undefined가 나오게 된다. 반대로 header component를 사용하면,
renderFnWithContext()라는 method가 return값으로 나오게 된다.
이 조건을 이용하여 slot에 정의한 기본 content를 보여주거나, 보여주지 않을 수 있다.
```

```vue
//base-card.vue

<template>
  <div>
    <header v-if="$slots.header">
      <slot name="header">
        <h2>Thd Default</h2>
      </slot>
    </header>
    <slot></slot>
  </div>
</template>

<script>
export default {
  name: "BaseCard",

  mounted() {
    console.log(this.$slots.header);
  }
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

section header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide
