# migrating a bit component

userList component 변경하기

```vue
//UserList.vue

<template>
  <base-container>
    <h2>Active Users</h2>
    <base-search @search="updateSearch" :search-term="enteredSearchTerm"></base-search>
    <div>
      <button @click="sort('asc')" :class="{selected: sorting === 'asc'}">Sort Ascending</button>
      <button @click="sort('desc')" :class="{selected: sorting === 'desc'}">Sort Descending</button>
    </div>
    <ul>
      <user-item
        v-for="user in displayedUsers"
        :key="user.id"
        :user-name="user.fullName"
        :id="user.id"
        @list-projects="$emit('list-projects', $event)"
      ></user-item>
    </ul>
  </base-container>
</template>

<script>
import UserItem from './UserItem.vue';

export default {
  components: {
    UserItem,
  },
  props: ['users'],
  data() {
    return {
      enteredSearchTerm: '',
      activeSearchTerm: '',
      sorting: null,
    };
  },
  computed: {
    availableUsers() {
      let users = [];
      if (this.activeSearchTerm) {
        users = this.users.filter((usr) =>
          usr.fullName.includes(this.activeSearchTerm)
        );
      } else if (this.users) {
        users = this.users;
      }
      return users;
    },
    displayedUsers() {
      if (!this.sorting) {
        return this.availableUsers;
      }
      return this.availableUsers.slice().sort((u1, u2) => {
        if (this.sorting === 'asc' && u1.fullName > u2.fullName) {
          return 1;
        } else if (this.sorting === 'asc') {
          return -1;
        } else if (this.sorting === 'desc' && u1.fullName > u2.fullName) {
          return -1;
        } else {
          return 1;
        }
      });
    },
  },
  methods: {
    updateSearch(val) {
      this.enteredSearchTerm = val;
    },
    sort(mode) {
      this.sorting = mode;
    },
  },
  watch: {
    enteredSearchTerm(val) {
      setTimeout(() => {
        if (val === this.enteredSearchTerm) {
          this.activeSearchTerm = val;
        }
      }, 300);
    }
  },
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>
```

```
components 와 props는 변경하지 않는다. 처음으로는 data에 선언된 property를 ref를 통해 변경한다.
초기값은 data에 선언한 값과 동일하게 빈 문자열 또는 null을 사용한다.
```

```js
setup(props, ) {
  const enteredSearchTerm = ref('');
  const activeSearchTerm = ref('');


  const sorting = ref(null);

},
```

```
그 다음으로는 computed 속성을 변경한다.
```

```js
availableUsers() {
  let users = [];
  if (this.activeSearchTerm) {
    users = this.users.filter((usr) =>
      usr.fullName.includes(this.activeSearchTerm)
    );
  } else if (this.users) {
    users = this.users;
  }
  return users;
},


//composition api
const availableUsers = computed(function () {
  let users = [];
  if (activeSearchTerm.value) {
    users = props.users.filter((usr) =>
        usr.fullName.includes(activeSearchTerm.value)
    );
  } else if (props.users) {
    users = props.users;
  }
  return users;
});
```

```
그대로 복사하여 setup method에 가져와서 this로 선언된 값들을 value로 변경해준다. 아래도 동일하다.
```

```js
displayedUsers() {
  if (!this.sorting) {
    return this.availableUsers;
  }
  return this.availableUsers.slice().sort((u1, u2) => {
    if (this.sorting === 'asc' && u1.fullName > u2.fullName) {
      return 1;
    } else if (this.sorting === 'asc') {
      return -1;
    } else if (this.sorting === 'desc' && u1.fullName > u2.fullName) {
      return -1;
    } else {
      return 1;
    }
}); 



//composition api
const displayedUsers = computed(function () {
  if (!sorting.value) {
    return availableUsers.value;
  }
  return availableUsers.value.slice().sort((u1, u2) => {
    if (sorting.value === 'asc' && u1.fullName > u2.fullName) {
      return 1;
    } else if (sorting.value === 'asc') {
      return -1;
    } else if (sorting.value === 'desc' && u1.fullName > u2.fullName) {
      return -1;
    } else {
      return 1;
    }
  });
```

```
method 변경하기
```

```js

updateSearch(val) {
  this.enteredSearchTerm = val;
},

function updateSearch(val) {
  enteredSearchTerm.value = val;
}
```

```js
sort(mode) {
  this.sorting = mode;
},

function sort(mode) {
  sorting.value  = mode;
}
```

```
watch 변경하기
```

```js
watch: {
  enteredSearchTerm(val) {
    setTimeout(() => {
      if (val === this.enteredSearchTerm) {
        this.activeSearchTerm = val;
      }
    }, 300);
  }
},


watch(enteredSearchTerm, function(newValue) {
    setTimeout(() => {
      if (newValue === enteredSearchTerm.value) {
        activeSearchTerm.value = newValue;
      }
    }, 300);
});
```


```vue
//UserList.vue

<template>
  <base-container>
    <h2>Active Users</h2>
    <base-search @search="updateSearch" :search-term="enteredSearchTerm"></base-search>
    <div>
      <button @click="sort('asc')" :class="{selected: sorting === 'asc'}">Sort Ascending</button>
      <button @click="sort('desc')" :class="{selected: sorting === 'desc'}">Sort Descending</button>
    </div>
    <ul>
      <user-item
        v-for="user in displayedUsers"
        :key="user.id"
        :user-name="user.fullName"
        :id="user.id"
        @list-projects="$emit('list-projects', $event)"
      ></user-item>
    </ul>
  </base-container>
</template>

<script>
import { ref, computed, watch } from 'vue'
import UserItem from './UserItem.vue';

export default {
  components: {
    UserItem,
  },
  props: ['users'],
  emits: ['list-projects'],

  setup(props, ) {
    const enteredSearchTerm = ref('');
    const activeSearchTerm = ref('');

    const availableUsers = computed(function () {
      let users = [];
      if (activeSearchTerm.value) {
        users = props.users.filter((usr) =>
            usr.fullName.includes(activeSearchTerm.value)
        );
      } else if (props.users) {
        users = props.users;
      }
      return users;
    });

    watch(enteredSearchTerm, function(newValue) {
        setTimeout(() => {
          if (newValue === enteredSearchTerm.value) {
            activeSearchTerm.value = newValue;
          }
        }, 300);
    });

    function updateSearch(val) {
      enteredSearchTerm.value = val;
    }

    const sorting = ref(null);

    const displayedUsers = computed(function () {
      if (!sorting.value) {
        return availableUsers.value;
      }
      return availableUsers.value.slice().sort((u1, u2) => {
        if (sorting.value === 'asc' && u1.fullName > u2.fullName) {
          return 1;
        } else if (sorting.value === 'asc') {
          return -1;
        } else if (sorting.value === 'desc' && u1.fullName > u2.fullName) {
          return -1;
        } else {
          return 1;
        }
      });
    });

    function sort(mode) {
      sorting.value  = mode;
    }

    return {
      enteredSearchTerm,
      activeSearchTerm,
      availableUsers,
      updateSearch,
      sorting,
      displayedUsers,
      sort,
    }
  },

  // data() {
  //   return {
  //     enteredSearchTerm: '',
  //     activeSearchTerm: '',
  //     sorting: null,
  //   };
  // },

  computed: {
    // availableUsers() {
    //   let users = [];
    //   if (this.activeSearchTerm) {
    //     users = this.users.filter((usr) =>
    //       usr.fullName.includes(this.activeSearchTerm)
    //     );
    //   } else if (this.users) {
    //     users = this.users;
    //   }
    //   return users;
    // },
    // displayedUsers() {
    //   if (!this.sorting) {
    //     return this.availableUsers;
    //   }
    //   return this.availableUsers.slice().sort((u1, u2) => {
    //     if (this.sorting === 'asc' && u1.fullName > u2.fullName) {
    //       return 1;
    //     } else if (this.sorting === 'asc') {
    //       return -1;
    //     } else if (this.sorting === 'desc' && u1.fullName > u2.fullName) {
    //       return -1;
    //     } else {
    //       return 1;
    //     }
    //   });
    // },
  },
  methods: {
    // updateSearch(val) {
    //   this.enteredSearchTerm = val;
    // },
    // sort(mode) {
    //   this.sorting = mode;
    // },
  },
  // watch: {
  //   enteredSearchTerm(val) {
  //     setTimeout(() => {
  //       if (val === this.enteredSearchTerm) {
  //         this.activeSearchTerm = val;
  //       }
  //     }, 300);
  //   }
  // },
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880338#overview
