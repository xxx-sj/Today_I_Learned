# components props with composition api

```
기존에 components를 생성하여 props를 데이터를 주고 받는 방식은 아래와 같았다.
```

```vue
//UserData.vue

<template>
  <h2> {{ userName }}</h2>
  <h3> {{ age }}</h3>
</template>

<script>
export default {
  name: "UserData",
  props: ['firstName', 'lastName', 'age'],
  computed: {
    userName() {
      return this.firstName + " " + this.lastName
    }
  },
}
</script>

<style scoped>

</style>
```

```vue
//App.vue

<template>
  <section class="container">
    <user-data :first-name="firstName" :last-name="lastName" :age="age"></user-data>         //
    <button @click="setAge">Change Age</button>
    <div>
      <input type="text" placeholder="First Name" v-model="firstName"/>
      <input type="text" placeholder="Last Name" ref="lastNameInput"/>
      <button @click="setLastName">Set Last Name</button>
    </div>
  </section>
</template>

<script>
import { ref, reactive, computed, watch } from 'vue'
import UserData from "@/components/UserData";                                             //
export default {
  components: {UserData},                                                                 //
  setup() {
    // const uName = ref('Maximilian');
    const uAge = ref(31);
    const firstName = ref('');
    const lastName = ref('');
    const lastNameInput = ref(null);
    const user = reactive({
      name: "Maximilian",
      age: 31,
    });

    const uName = computed(function() {
      return firstName.value + ' ' + lastName.value;
    });

    watch([uAge, uName], (newValues, oldValues) => {
      console.log("old age:" + oldValues[0]);
      console.log("new Age:" + newValues[0]);
      console.log("old name:" + oldValues[1]);
      console.log("new name:" + newValues[1]);
    });

    // uName.value = 'Max';

    function setNewAge() {
      user.age = 32;
      uAge.value = 33;
    }

    function setLastName() {
      console.log("lastNameInput.value", lastNameInput.value);
      lastName.value = lastNameInput.value.value;
    }

    // function setFirstName(event) {
    //   firstName.value = event.target.value;
    // }
    //
    // function setLastName(event) {
    //   lastName.value = event.target.value;
    // }



    return { age: uAge,
      user: user,
      setAge: setNewAge,
      firstName,
      lastName,
      lastNameInput,
      setLastName,
      userName: uName }
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

  // watch: {
  // age(val) {
  //  console.log(val);
//    }
  // }

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
composition api를 사용한다고 하여 option api를 사용하지 못하는 것은 아니다. composition api와 option api를 동시에 사용할 수 있다.
이번에는 props로 전달받은 값을 핸들링하여 composition api로 나타낸다. component에서 props로 받은 데이터를 option api로 나타낼 때는 
this를 사용하여 props 데이터에 접근이 가능했지만, composition api를 이용하여 computed를 정의할 때는 this를 사용할 수 없다. setup method에 정의할 때
setup 메소드는 vue에 의해 vue instance가 아직 정의되기 전에 호출되기 때문에 this가 없을 수 있다. 그럼에도 composition api로 props를 사용하는 방법은
setup method의 인자를 이용하는 것인데, setup method는 실제 method 이기 때문에 2개의 인자를 받는다. 첫 번째 인자로 받는값이 props로 이루어진 object 이다
이 값을 이용하여 props에 접근할 수 있으며, parent에서 전달한 props 값이 변경될 때마다 child 에서 computed로 선언한 값에 의해 reactive하게 값을 변경한다. 
```

```vue
//UserData.vue

<template>
  <h2> {{ userName }}</h2>                //
  <h3> {{ age }}</h3>
</template>

<script>
import {computed} from 'vue'
export default {
  name: "UserData",
  props: ['firstName', 'lastName', 'age'],           //
  setup(props,) {                                  //
    const uName = computed(function() {           //
      return props.firstName + '' + props.lastName;     //
    });

    return { userName: uName };                    //
  },
  // computed: {
  //   userName() {
  //     return this.firstName + " " + this.lastName
  //   }
  // },
}
</script>
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880208#content
