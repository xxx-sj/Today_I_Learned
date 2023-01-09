# replacing data with refs

```
data, methods, computed, watch는 결국 setup method에 묶여 return 합니다. 반면에 propst, emits, components는 
composition api에 의해 변경되지않고 남아있습니다. 아래에 샘플 app.vue component를 composition api를 이용하여 
변경한다.
``` 

```vue
//App.vue

<template>
  <section class="container">
    <h2>{{ userName }}</h2>
  </section>
</template>

<script>
export default {
  data() {
    return {
      userName: 'Maximilian',
    };
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
composition api는 vue 3부터 사용할 수 있다.
시작으로는 options에 setup() 이라는 method을 정의하고 { ref }를 vue에서 import하여 가져온다.
ref는 setup 내에서 사용되는 메소드이다. ref는 이전 refs와 같이 dom element를 참조하는 것이 아닌 template 내 값을
참조할 때 사용한다 setup내에서는 this를 사용하지 않는데, vue component가 초기화 되기 이전에 setup을 사용하기 때문에 this에 대한
access가 아직 없는 상태일 수 있다.
대신 setup block 내에서 const라던가, let을 사용하여 ref() function의 값을 저장하며, ref()의 인자로는 실제로 store하고 싶은 값을 넘긴다.
ref로 전달된 값은 object로 만들어지며 vue는 해당 값을 감시하게된다. 기존 값과는 다른값을 할당하게 되면 vue는 이를 감지하여 dom을 업데이트한다.
그리고나서 setup method는 {} [object] 를 반환하는데 setup block내에서 정의한 변수나, function들을 object 형식으로 return 한다.
return object의 key는 dom에서 참조하는 값이며, value는 key에 대한 값이다.
```

```vue
//App.vue

<template>
  <section class="container">
    <h2>{{ userName }}</h2>
  </section>
</template>

<script>
import { ref } from 'vue'
export default {
  setup() {
    const userName = ref('Maximilian');
    return { userName: userName,  }
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

```js
setup() {
    let userName = ref('Maximilian');

    setTimeout(() => {
      userName = 'MAx';
    }, 2000);
    return { userName: userName,  }
  },
```

```
const/let 로 정의한 data값을 setTimeout을 이용하여 변경하게 하면 vue는 변경을 감지하지 못한다
setup method는 한번만 실행되며 변경된 값에 대하여 새로운 object를 return 받을 수 없다.
const로 정의한 이유가 여기에 있다. setup method는 한번만 실행되며 변경되지 않기 때문에 상수로 정의한 것이다.
대신 userName.value로 값을 변경하여 dom을 update할 수 있다. ref는 object를 생성하는데, 그 안에 value라는 property가 존재한다.
value는 ref function의 인자로 전달한 값이 value로 저장되는 것이다. template에 값을 전달한다고 하여 data.value로 쓸 필요는 없다
vue는 자동으로 해당 변수의 value property를 읽어 렌더링하기 때문이다.
```

```vue
//App.vue

<template>
  <section class="container">
    <h2>{{ userName }}</h2>
  </section>
</template>

<script>
import { ref } from 'vue'
export default {
  setup() {
    const userName = ref('Maximilian');
    console.log({userName})

    setTimeout(() => {
      console.log({userName})
      userName.value = 'MAx';
    }, 3000);
    return { userName: userName,  }
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

```
또한 export default 사용이 아닌 script 태그에 setup을 추가하여 선언할 수 있다.
```

```js
<script setup>
import { ref } from 'vue';
 
const uName = ref('Maximilian');
 
setTimeout(function() {
  uName.value = 'Max';
}, 2000);
</script>
```

![image](https://user-images.githubusercontent.com/62305110/211332505-23891a2e-08c8-40d7-8386-567783545768.png)


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880118#content
