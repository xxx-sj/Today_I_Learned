# how to use template refs

```
options api에서 templte에 ref 속성을 사용하여 접근하려고 하면 [ this.$refs[ ref-name ] ] 으로 사용하여 접근했었다 .
composition api에서는 아래와 같이 사용한다.
```

```vue
<template>
  <section class="container">
    <h2>{{ userName }}</h2>
    <h3>{{ age }}</h3>
    <button @click="setAge">Change Age</button>
    <div>
      <input type="text" placeholder="First Name" v-model="firstName"/>
      <input type="text" placeholder="Last Name" ref="lastNameInput"/>        //
      <button @click="setLastName">Set Last Name</button>
    </div>
  </section>
</template>

<script>
import { ref, reactive, computed, watch } from 'vue'
export default {
  setup() {
    // const uName = ref('Maximilian');
    const uAge = ref(31);
    const firstName = ref('');
    const lastName = ref(''); 
    const lastNameInput = ref(null);                               //
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

    function setLastName() {                                     //
      lastName.value = lastNameInput.value.value;                 //
    }                                                           //

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
      lastNameInput,                                    //
      setLastName,                                      //
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
처음 ref를 통해 wrpping object를 만들 때 null로 초기화한다. 참조하는 template가 없기 때문이다. 이 후 동일하게
setup method의 return 값으로 method와 선언한 ref 를 return한다. 참조하고 싶은 element에 ref 속성으로 return으로 넘긴 
property를 넘겨주고, method같은 곳에서 사용할 때는 [ refObjectName.value.value ] 로 참조한다.
기존에도 ref로 생성한 변수의 값은 value에 담겨져 있고, ref에 사용 시 value의 값은 template 의 포인터가 되고, 포인터가 가리키는 
template의 값을 사용하고 싶다면, value를 사용하면 된다.
```
![image](https://user-images.githubusercontent.com/62305110/212520813-1335ba48-d8c1-40bb-bef5-ecda93d83173.png)


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880204#content
