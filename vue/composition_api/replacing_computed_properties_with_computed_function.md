# replacing computed properties with the computed function

```
기존 option api에서 computed는 아래와 같이 사용하였다.
```

``` js
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
```

```
composition api에서는 vue에서 computed를 import 받아 setup method에 정의한다.
```

```vue
//App.vue

<template>
  <section class="container">
    <h2>{{ userName }}</h2>                                             //
    <h3>{{ age }}</h3>
    <button @click="setAge">Change Age</button>
    <div>
      <input type="text" placeholder="First Name" @input="setFirstName"/>  //
      <input type="text" placeholder="Last Name" @input="setLastName"/>   //
    </div>
  </section>
</template>

<script>
import { ref, reactive, computed } from 'vue'
export default {
  setup() {
    // const uName = ref('Maximilian');
    const uAge = ref(31); 
    const firstName = ref('');            //
    const lastName = ref('');             //
    const user = reactive({
      name: "Maximilian",
      age: 31,
    });

    const uName = computed(function() {                  //
      return firstName.value + ' ' + lastName.value;      //
    });                                                  //

    uName.value = 'Max';                          // x

    function setNewAge() {
      user.age = 32;
      uAge.value = 33;
    }

    function setFirstName(event) {                             //
      firstName.value = event.target.value;                    //
    }

    function setLastName(event) {                            //
      lastName.value = event.target.value;                  //
    }



    return { age: uAge, user: user, setAge: setNewAge, setFirstName, setLastName, userName: uName }
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
computed method내에 인자로 function을 전달한다. function은 이전에 option api에서 사용하던 computed의 property중 하나에 해당되며,
이전과 마찬가지로 computed 속성을 vue가 감시하다가 변경이 일어나면 template를 re-rendering한다. 이 예제에서는 input을 통해 받은 event를 가지고
ref로 선언한 firstName와 lastName의 값을 변경하며, computed 속성으로 template에 표현한다. 이 전 method에서 마찬가지로 ref의 변수를 가리키며 [ not this ]
ref로 선언한 값은 wrpping되어있기 때문에 변수의 value값을 사용한다. computed 또한 변수에 담아 setup method의 return 값으로 전달하게 되며, computed 속성은
only read에 해당된다.
```

![image](https://user-images.githubusercontent.com/62305110/212482380-6eb7f816-8dad-4878-b0a9-202924921ad7.png)


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880244#content
