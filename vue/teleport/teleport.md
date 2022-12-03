# teleport [vue3 기능]

```
입력에 대해 잘못된 입력일 경우 오류를 나타내는 component를 작성한다고 한다면, 아래와 같다.
```

```vue
//errorAlert.vue

<template>
  <dialog open>
    <slot></slot>
  </dialog>
</template>

<script>
export default {
  name: "ErrorAlert"
}
</script>

<style scoped>
dialog {
  margin: 0;
  position: fixed;
  top: 20vh;
  left: 30%;
  width: 40%;
  background-color: white;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  padding: 1rem;
}
</style>
```

```vue
//manageGoals.vue

<template>
  <div>
    <h2>Manage Goals</h2>
    <input type="text" ref="goal"/>
    <button @click="setGoal">Set Goal</button>
    <error-alert v-if="inputIsInvalid">
      <h2>Input is invalid!</h2>
      <p>Please enter at least a few characters...</p>
      <button @click="confirmError">Okay</button>
    </error-alert>
  </div>
</template>

<script>
import ErrorAlert from "@/components/ErrorAlert";
export default {
  name: "ManageGoals",
  components: {ErrorAlert},
  data() {
    return {
      inputIsInvalid: false,
    };
  },
  methods: {
    setGoal() {
      const enteredValue = this.$refs.goal.value;
      if (enteredValue === '') {
        this.inputIsInvalid = true;
      }
    },
    confirmError() {
      this.inputIsInvalid = false;
    }
  }
}
</script>
```

```
여기서 볼 수 있는 slot의 장점은 논리적인 부분은 slot에 전달하기 전인 즉, slot을 정의한 태그를 사용하는 [manageGoal] 곳에서 
정의할 수 있고, 렌더링은 errorAlert slot을 정의한 부분에 포함되어 렌더링된다.
위에서 볼 수 있듯이 manageGoal에서 errorAlert component를 사용하고 있는데, 문제가 하나가 있다. 
errorAlert은 manageGoal에 포함되어 렌더링되기 때문에 alert의 의도와는 다르게 동작하게 된다 .
[alert이라 함은 전체 페이지에서 렌더링되야 하기 때문이다.]
이러한 것을 해결하기 위해 vue에서는 teleport라는 태그를 제공한다.
teleport는 one prop, one attribute를 필요로 하는데 
one prop은 to 이다. to 에는 css selector를 값으로 입력하며, 전체 페이지 중 실제로 포함되어 렌더링 될 곳을 지정하면 된다.
[body, #app, etc..]
```

```vue
//manageGoal.vue

<template>
  <div>
    <h2>Manage Goals</h2>
    <input type="text" ref="goal"/>
    <button @click="setGoal">Set Goal</button>
    <teleport to="body">
      <error-alert v-if="inputIsInvalid">
        <h2>Input is invalid!</h2>
        <p>Please enter at least a few characters...</p>
        <button @click="confirmError">Okay</button>
      </error-alert>
    </teleport>
  </div>
</template>

<script>
import ErrorAlert from "@/components/ErrorAlert";
export default {
  name: "ManageGoals",
  components: {ErrorAlert},
  data() {
    return {
      inputIsInvalid: false,
    };
  },
  methods: {
    setGoal() {
      const enteredValue = this.$refs.goal.value;
      if (enteredValue === '') {
        this.inputIsInvalid = true;
      }
    },
    confirmError() {
      this.inputIsInvalid = false;
    }
  }
}
</script>
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide
