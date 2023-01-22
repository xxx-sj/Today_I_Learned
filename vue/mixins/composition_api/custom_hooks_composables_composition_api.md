# custom hooks / composables and the composition api

여기서 말하는 hooks, composables, custom compostiion functions들은 모두 같은것을 의미한다.
composition api를 사용하여 mixin과 같이 다른 js 파일에 setup method 내부 logic을 정의한다.
이 후 사용하는 component setup method 블록에서 정의한js 파일의 function을 call하고 결과값을 setup 메소드의 return 값으로
사용한다.

![image](https://user-images.githubusercontent.com/62305110/213900113-4a90916c-9ddf-4163-a09b-ca413cdfb8db.png)


```js
// hooks/alert.js

import {ref} from "vue";

export default function useAlert() {
    const alertIsVisible = ref(false);

    function showAlert() {
        alertIsVisible.value = true;
    }
    function hideAlert() {
        alertIsVisible.value = false;
    }

    return [
        alertIsVisible,
        showAlert,
        hideAlert
    ];
}
```

```vue
//AddUser.vue

<template>
  <user-alert v-if="alertIsVisible" title="Add a User?" @close="hideAlert">
    <p>Do you want to continue with adding a user?</p>
  </user-alert>
  <section>
    <h2>Add a User</h2>
    <button @click="showAlert">Add User</button>
  </section>
</template>

<script>
import UserAlert from './UserAlert.vue';
import useAlert from "@/hooks/alert";

export default {
  components: {
    UserAlert,
  },
  setup() {
    // const alertArr = useAlert();
    const [ alertIsVisible, showAlert, hideAlert ] = useAlert();

    // return {...alertArr};
    return {
      alertIsVisible,
      showAlert,
      hideAlert
    };
  },
};
</script>
```

```vue
// DeleteUser.vue


<template>
  <user-alert v-if="alertIsVisible" title="Delete the User?" @close="hideAlert">
    <p>Do you want to continue with deleting a user?</p>
  </user-alert>
  <section>
    <h2>Delete a User</h2>
    <button @click="showAlert">Delete User</button>
  </section>
</template>

<script>
import UserAlert from './UserAlert.vue';
import useAlert from "@/hooks/alert";

export default {
  components: {
    UserAlert,
  },
  setup() {
    // const alertArr = useAlert();
    const [ alertIsVisible, showAlert, hideAlert ] = useAlert();

    // return {...alertArr};
    return {
      alertIsVisible,
      showAlert,
      hideAlert
    };
  },
};
</script>
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880380#content
