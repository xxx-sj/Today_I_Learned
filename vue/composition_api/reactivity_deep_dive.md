# Reactivity A deep dive

```
vue에 reactive 값인지 판별해주는 isReactive와 isRef가 있다.
해당 메소드들을 사용하여 변수의 value값을 확인해보면 모두 false인 것을 확인할 수 있다.
```

```vue

<template>
  <section class="container">
    <h2>{{ user.name }}</h2>
    <h3>{{ user.age }}</h3>
    <p>{{ age }}</p>
  </section>
</template>

<script>
import { ref, reactive, isReactive, isRef } from 'vue'
export default {
  setup() {
    // const uName = ref('Maximilian');
    const uAge = ref(31);
    const user = reactive({
      name: "Maximilian",
      age: 31,
    });

    console.log(isRef(uAge.value));
    console.log(isReactive(user.value.name), user.age);

    setTimeout(() => {
      console.log({user})
      // uName.value = 'MAx';
      // uAge.value = 32;
      user.name = 'max';
      user.age = 32;
    }, 3000);

    return { age: uAge, user }
  },

};
</script>

<style>
* {
  box-sizing: border-box;
}

html {
  font-family: sans-serif;
}

body {
  margin: 0;
}

.container {
  margin: 3rem auto;
  max-width: 30rem;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  padding: 1rem;
  text-align: center;
}
</style>
```

```
이와 반대로 uAge와 user를 매개변수로 넘겨보면 true를 반환하는데, 이 전에 했던 setup return 값으로 value를 넘기면 
reactive하게 값이 렌더링되지 않은 이유가 이 이유와 같다. 객체를 넘기면 vue가 값의 변경을 감지하여 다시 렌더링 하지만 value 값을
직접 접근하여 변경하면 스냅샷 [ console.log() ]의 값은 변하겠지만 리 렌더링되지 않는다. value의 값은 그저 string or 어떠한 값에 불과하다.
```

```
vue의 toRefs func을 사용하여 중첩된 object의 property 값을 reactive하게 변경할 수 있다. toRefs에 object를 매개변수로 전달하게 되면 
object의 property들은 각각 ref가 되어 setTimeout에서 값을 변경해도 reactive하게 반영되여 리 렌더링되게 된다.
```

```vue
<template>
  <section class="container">
    <h2>{{ userName }}</h2>
    <h3>{{ age }}</h3>
  </section>
</template>

<script>
import { ref, reactive, toRefs } from 'vue'
export default {
  setup() {
    // const uName = ref('Maximilian');
    const uAge = ref(31);
    const user = reactive({
      name: "Maximilian",
      age: 31,
    });

    console.log(uAge);
    console.log(user, user.age);

    setTimeout(() => {
      console.log({user})
      // uName.value = 'MAx';
      // uAge.value = 32;
      user.name = 'max';
      user.age = 32;
    }, 3000);

    const userRefs = toRefs(user);

    return { user: user, userName: userRefs.name, age: userRefs.age }
  },

};
</script>

<style>
* {
  box-sizing: border-box;
}

html {
  font-family: sans-serif;
}

body {
  margin: 0;
}

.container {
  margin: 3rem auto;
  max-width: 30rem;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  padding: 1rem;
  text-align: center;
}
</style>
```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880124#content
