# more custom composition function

hooks는 mixins과는 다르게 일반적인 js function이기 때문에 인자를 받아서 처리할 수 있다.
만약 ref의 시작 값을 받아서 처리하고 싶다면 인자로 ref의 값을 받아 할당할 수 있다.
이처럼 일반 mixins보다는 좀 더 유연하게 데이터를 다를 수 있다.


```js
// hooks/alert.js
import {ref} from "vue";

export default function useAlert(startingVisibility = false, ) {
    const alertIsVisible = ref(startingVisibility);

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
// components/DeleteUser.vue

<template>
  <user-alert v-if="alertIsVisible" :title="alertTitle" @close="hideAlert">
    <p>Do you want to continue with deleting a user?</p>
  </user-alert>
  <section>
    <h2>Delete a User</h2>
    <button @click="showAlert">Delete User</button>
  </section>
</template>

<script>
import {ref} from 'vue';
import UserAlert from './UserAlert.vue';
import useAlert from "@/hooks/alert";

export default {
  components: {
    UserAlert,
  },
  setup() {
    const alertTitle = ref('Delete User?');
    // const alertArr = useAlert();
    const [ alertIsVisible, showAlert, hideAlert ] = useAlert(true);

    // return {...alertArr};
    return {
      alertIsVisible,
      showAlert,
      hideAlert,
      alertTitle,
    };
  },
};
</script>
```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880382#overview
