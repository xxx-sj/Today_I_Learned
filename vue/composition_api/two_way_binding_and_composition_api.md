# two-way binding and the composition api [ v-model ]

```
composition api에서도 v-model을 사용할 수 있는데, 이 전 data에 선언한 값을 v-model에 사용하면 된다.
```

``` vue
<template>
  <section class="container">
    <h2>{{ userName }}</h2>
    <h3>{{ age }}</h3>
    <button @click="setAge">Change Age</button>
    <div>
      <input type="text" placeholder="First Name" v-model="firstName"/>          //
      <input type="text" placeholder="Last Name" v-model="lastName"/>            //
    </div>
  </section>
</template>

<script>
import { ref, reactive, computed } from 'vue'
export default {
  setup() {
    // const uName = ref('Maximilian');
    const uAge = ref(31);
    const firstName = ref('');                        //
    const lastName = ref('');                        //
    const user = reactive({
      name: "Maximilian",
      age: 31,
    });

    const uName = computed(function() {
      return firstName.value + ' ' + lastName.value;
    });

    // uName.value = 'Max';

    function setNewAge() {
      user.age = 32;
      uAge.value = 33;
    }

    // function setFirstName(event) {
    //   firstName.value = event.target.value;
    // }
    //
    // function setLastName(event) {
    //   lastName.value = event.target.value;
    // }



    return { age: uAge, user: user, setAge: setNewAge, firstName, lastName, userName: uName }      //
  },

  // date() {
  //   return {
  //     userName: 'Maximilian',
  //     age: 31
  //   }
  // },

  // methods: {
  //   setNewAge() {
  //     this.age = 32;
  //   },
  // },

  // computed: {},

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
ref로 선언한 값을 setup method return object에 선언하고 template에서 사용하는 것은 동일하다. 여기서 주의해야 할 점은
이 전과 같이 refs object의 value를 사용하는 것이 아닌 ref wrpping object를 사용하는 것이다.
```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880248#content
