# use mixins

여러 개의 컴포넌트 내에 겹치는 options [data, method, computed] 있다면 mixins을 사용하여 하나의 js 파일에 중복되는 코드를 정의하고 
정의된 코드를 각각의 component 에서 mixins attr을 사용하여 마치 component에 정의한 것처럼 사용할 수 있다.


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
import alertMixin from '../mixins/alert'

export default {
  mixins: [alertMixin],
};
</script>
```

```vue
//DeleteUser.vue

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
import alertMixin from '../mixins/alert'

export default {
  mixins: [alertMixin],
};
</script>
```

```js
// mixins/alert.js

import UserAlert from "@/components/UserAlert";

export default {
    components: {
        UserAlert,
    },
    data() {
        return {
            alertIsVisible: false,
        };
    },
    methods: {
        showAlert() {
            this.alertIsVisible = true;
        },
        hideAlert() {
            this.alertIsVisible = false;
        },
    },
};
```
여기서 주의해야 할 점은 components는 mixins에서 공유할 수 없기 때문에 각각의 component에서 선언해주어야 한다.

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
import alertMixin from '../mixins/alert'
import UserAlert from "@/components/UserAlert";

export default {
  components: {
    UserAlert,
  },
  mixins: [alertMixin],
};
</script>
```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880370#content
