# building reactive objects

```
새로운 reative data를 추가할 수 있다. 이번에는 age를 추가한다.
```

```vue
<template>
  <section class="container">
    <h2>{{ userName }}</h2>
    <h3>{{ age }}</h3>                     //
  </section>
</template>

<script>
import { ref } from 'vue'
export default {
  setup() {
    const uName = ref('Maximilian');
    const uAge = ref(31);                  //
    console.log({uName})

    setTimeout(() => {
      console.log({uName})
      uName.value = 'MAx';                 
      uAge.value = 20;                     //
    }, 3000);
    return { userName: uName,  age: uAge}        //
  },
  // data() {
  //   return {
  //     userName: 'Maximilian',
  //   };
  // },
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
![image](https://user-images.githubusercontent.com/62305110/212470442-343cc3ee-f6ee-4e62-b215-1af16ff9c6f8.png)

```
추가한 값은 uAge로 기본값은 31이다. setup method로 return 하는 object에 key값으로는 template에서 사용할 값이며, 
value는 ref의 값이다. 2초 후 setTimeout을 이용해 ref object의 값 value를 변경한다.
```

```
동일한 성질의 값은 객체로 묶어서 표현할 수 있다.
```

```js

<script>
import { ref } from 'vue'
export default {
  setup() {
    // const uName = ref('Maximilian');
    // const uAge = ref(31);
    const user = ref({
      name: "Maximilian",
      age: 31,
    });
    console.log({user})
    setTimeout(() => {
      console.log({user})
      // uName.value = 'MAx';
      // uAge.value = 32;
      user.value.name = 'max';
      user.value.age = 32;
    }, 3000);
    return { userName: user.value.name,  age: user.value.age }
  },
};
</script>
```

```
ref로 user 객체를 만들어서 값을 설정하고, user 객체의 value의 값으로 [ ref를 통해 값을 생성하면 ref는 object를 반환하고 object 내부 value의 값이 
표현되는 값이다. ] 2초 후 값을 변경해도 재 렌더링되지 않는다. vue는 해당 ref object의 value값에 대한 read and write를 추적하며, 값을 변경한다. [불확실]
그리고 ref의 값으로 object 를 전달하면 해당 object는 proxy로 감싸져 vue가 내부의 변경사항을 추적할 수 있다. 하지만 그럼에도 위와 같이 user.value.[property]
에 접근하여 값을 변경하는 것으로 화면을 업데이트 할 수 없었다. 문제는 어떻게 template에 전달하는 가 이다. setup method에서 return 값으로 또한 
user.value.[property] 처럼 깊은 값을 return 하고 있다. value의 property는 그저 string과 number일 뿐이다. reactive value가 아니다. 띠리서 값을 변경해도
변경 사항을 추적할 수 없다. 따라서 return 값으로는 ref의 object를 그대로 전달하고 template에서 해당 property의 값을 꺼내어 사용해야한다.
```

```
<template>
  <section class="container">
    <h2>{{ user.name }}</h2>
    <h3>{{ user.age }}</h3>
  </section>
</template>

<script>
import { ref } from 'vue'
export default {
  setup() {
    // const uName = ref('Maximilian');
    // const uAge = ref(31);
    const user = ref({
      name: "Maximilian",
      age: 31,
    });
    console.log(user)
    setTimeout(() => {
      console.log({user})
      // uName.value = 'MAx';
      // uAge.value = 32;
      user.value.name = 'max';
      user.value.age = 32;
    }, 3000);
    return { userName: user.value.name,  age: user.value.age, user:user }
  },
};
</script>
```

![image](https://user-images.githubusercontent.com/62305110/212471217-0913581e-7142-437d-bbb0-a0d2f10e3226.png)


```
ref의 값으로 object를 사용해야 한다면, ref보다 reative를 사용하는 것이 좋다. reactive는 ref와 비슷하지만 
명시적으로 object를 위해 만들어졌습니다. reative를 사용하려면 object를 넘겨야 하며, ref와는 다르게 wrapping object가 없어서
user.value.[property] 처럼 value property에 접근할 필요없이 객체의 값에 접근할 수 있다. [ user.[property] ] ref와 동일하게 obejct를 return 한다.
```

```js
<script>
// import { ref } from 'vue'
import { reactive } from 'vue'                    //
export default {
  setup() {
    // const uName = ref('Maximilian');
    // const uAge = ref(31);
    const user = reactive({                      //
      name: "Maximilian",                        //
      age: 31,                                   //
    });
    console.log(user)
    setTimeout(() => {
      console.log({user})
      // uName.value = 'MAx';
      // uAge.value = 32;
      user.name = 'max';                         //
      user.age = 32;                            //
    }, 3000);
    return {  user:user }                      //
  },
  // data() {
  //   return {
  //     userName: 'Maximilian',
  //   };
  // },
};
</script>
```



##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880122#content
