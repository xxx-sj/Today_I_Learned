# working with provide / inject

```
provide는 child component에 prop으로 전달하는 값 이외에 provide를 이용하여 제공할 때 사용한다.
option api에서는 provide를 사용할 때 아래와 같이 사용하였다. 
```

```js
provide() {
  return {age: this.age}
}
```

```
composition api에서는 먼저 vue에서 provide를 import 받아서 provide로 제공할 변수를 선언한 이 후 라인에 provide function을 작성한다.
provide func의 첫 번째 인자로는 provide로 전달할 값의 key 값이다. inject로 제공받은 값을 사용할 때 사용하는 key 값에 해당하며,
두 번재 인자로는 key값의 value값 이다. 실제 전달할 value의 값 [ 변수 ]를 인자로 전달한다.
```

```vue
//App.vue


<script>
import { ref, reactive, computed, watch, provide } from 'vue'
import UserData from "@/components/UserData";
export default {
  components: {UserData},
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

    provide('userAge', uAge);

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
  // },

  // provide() {
  //   return {age: this.age}
  // }
};
</script>
```

```
inject 받는 곳에서는 vue에서 inject func을 import 받아 이 전에 provide에서 선언한 key를 가지고 value를 꺼낸다.
또한 inject 받은 곳에서 해당 값을 변경해서는 안된다. 유지보수 및 값 변경에 대한 예측이 불가능해진다.
```

```vue
//USerData.vue child

<template>
  <div>
    <h2> {{ userName }}</h2>
    <h3> {{ age }}</h3>
  </div>
</template>

<script>
import { computed, inject } from 'vue'
export default {
  name: "UserData",
  props: ['firstName', 'lastName'],
  setup(props, context) {
    const uName = computed(function() {
      return props.firstName + '' + props.lastName;
    });

    const age = inject('userAge')

    console.log(context)
    // context.emit('save-data', 1); // this.$emit('save-data', 1);

    return { userName: uName, age: age };
  },
  // computed: {
  //   userName() {
  //     return this.firstName + " " + this.lastName
  //   }
  // },
}
</script>
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880218#content
