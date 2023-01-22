# creating a search hook

```vue
// userList.vue

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
import { ref, computed } from 'vue';

import UserItem from './UserItem.vue';
import useSearch from "@/hooks/search";

export default {
  components: {
    UserItem,
  },
  props: ['users'],
  emits: ['list-projects'],
  setup(props) {
    const {enteredSearchTerm, availableItems, updateSearch} = useSearch(props.users, 'fullName');
    // const enteredSearchTerm = ref('');
    // const activeSearchTerm = ref('');
    //
    // const availableUsers = computed(function () {
    //   let users = [];
    //   if (activeSearchTerm.value) {
    //     users = props.users.filter((usr) =>
    //       usr.fullName.includes(activeSearchTerm.value)
    //     );
    //   } else if (props.users) {
    //     users = props.users;
    //   }
    //   return users;
    // });
    //
    // watch(enteredSearchTerm, function (newValue) {
    //   setTimeout(() => {
    //     if (newValue === enteredSearchTerm.value) {
    //       activeSearchTerm.value = newValue;
    //     }
    //   }, 300);
    // });
    //
    // function updateSearch(val) {
    //   enteredSearchTerm.value = val;
    // }

    const sorting = ref(null);
    const displayedUsers = computed(function () {
      if (!sorting.value) {
        return availableItems.value;
      }
      return availableItems.value.slice().sort((u1, u2) => {
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
      sorting.value = mode;
    }

    return {
      enteredSearchTerm,
      updateSearch,
      displayedUsers,
      sorting,
      sort
    };
  },
  // data() {
  //   return {
  //     enteredSearchTerm: '',
  //     activeSearchTerm: '',
  //     sorting: null,
  //   };
  // },
  // computed: {
  //   availableUsers() {
  //     let users = [];
  //     if (this.activeSearchTerm) {
  //       users = this.users.filter((usr) =>
  //         usr.fullName.includes(this.activeSearchTerm)
  //       );
  //     } else if (this.users) {
  //       users = this.users;
  //     }
  //     return users;
  //   },
  //   displayedUsers() {
  //     if (!this.sorting) {
  //       return this.availableUsers;
  //     }
  //     return this.availableUsers.slice().sort((u1, u2) => {
  //       if (this.sorting === 'asc' && u1.fullName > u2.fullName) {
  //         return 1;
  //       } else if (this.sorting === 'asc') {
  //         return -1;
  //       } else if (this.sorting === 'desc' && u1.fullName > u2.fullName) {
  //         return -1;
  //       } else {
  //         return 1;
  //       }
  //     });
  //   },
  // },
  // methods: {
  //   updateSearch(val) {
  //     this.enteredSearchTerm = val;
  //   },
  //   sort(mode) {
  //     this.sorting = mode;
  //   },
  // },
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

해당 component에서 주석에 해당하는 부분을 custom hook으로 빼서 사용한다.

```js

import {computed, ref, watch} from "vue";

export default function useSearch(items, searchProp) {
    const enteredSearchTerm = ref('');
    const activeSearchTerm = ref('');

    const availableItems = computed(function () {
        let filteredItems = [];
        if (activeSearchTerm.value) {
            filteredItems = items.filter((item) =>
                item[searchProp].includes(activeSearchTerm.value)
            );
        } else if (items) {
            filteredItems = items;
        }
        return filteredItems;
    });

    watch(enteredSearchTerm, function (newValue) {
        setTimeout(() => {
            if (newValue === enteredSearchTerm.value) {
                activeSearchTerm.value = newValue;
            }
        }, 300);
    });

    function updateSearch(val) {
        enteredSearchTerm.value = val;
    }

    return {
        enteredSearchTerm,
        activeSearchTerm,
        availableItems,
        updateSearch,
    }
}
```

이 전에 props로 받아서 처리하던 값은 function 인자로 받아서 사용하고, filter 부분의 property name 같은 경우는
다른 component 내에서도 겹치는 logic이 있을경우 filter의 property-name은 동적으로 사용할 수 있게 받아서 사용한다.
또는 2번 째인자로 function을 받아서 filter에 callback method로 넘긴다.

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880388#content
