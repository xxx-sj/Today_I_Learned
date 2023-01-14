# replacing "methods" with regular functions

```
option.api를 사용하여 button click 하면 data의 age를 변경하는 코드는 아래와 같다. option.api는 기존에 vue에서 사용하던
표현 방식이다.
```

```js
// date() {
  //   return {
  //     userName: 'Maximilian',
  //     age: 31
  //   }
  // },

  methods: {
    setNewAge() {
      this.age = 32;
    },
  }
```

```
이제 composition api로 변경해 본다. composition api에서 setup method는 vue가 해당 component를 set up 할 때 한번만 call 된다.
setup method는 기본적으로 template에서 사용할 data를 정의한다. 또한 function을 정의하고 return을 통해 function의 pointer를 return 할 수 있다.
```

```vue

<template>
  <section class="container">
    <h2>{{ user.name }}</h2>
    <h3>{{ user.age }}</h3>
    <button @click="setAge">Change Age</button>
  </section>
</template>

<script>
import { ref, reactive } from 'vue'
export default {
  setup() {
    // const uName = ref('Maximilian');
    const uAge = ref(31);
    const user = reactive({
      name: "Maximilian",
      age: 31,
    });

    function setNewAge() {
      user.age = 32;
    }

    console.log(uAge);
    console.log(user, user.age);


    return { user: user, setAge: setNewAge }
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
  // }

};
</script>
```

```
setup method내에 function을 정의하고 해당 값을 return하는 object에 담는다. key는 tempalte에서 사용할 name이고, value는 정의한 func name이다.
return 할 때 oebject에서 함수를 exec[실행 ()] 하지않고 pointer만 넘긴다. setup의 return 값으로는 data가 될 수 있고, function이 될수도 있다.
ref에 대해서도 적용할 수 있다.
```

```j<script>
import { ref, reactive } from 'vue'
export default {
  setup() {
    // const uName = ref('Maximilian');
    const uAge = ref(31);
    const user = reactive({
      name: "Maximilian",
      age: 31,
    });

    function setNewAge() {
      user.age = 32;
      uAge.value = 33;
    }

    console.log(uAge);
    console.log(user, user.age);


    return { age: uAge, user: user, setAge: setNewAge }
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
  // }

};
</script>
s

```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880130#content
