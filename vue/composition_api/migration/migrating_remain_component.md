# migrating remain component

```
projectList 변경하기
```

```vue
//ProjectList.vue

<template>
  <base-container v-if="user">
    <h2>{{ user.fullName }}: Projects</h2>
    <base-search v-if="hasProjects" @search="updateSearch" :search-term="enteredSearchTerm"></base-search>
    <ul v-if="hasProjects">
      <project-item v-for="prj in availableProjects" :key="prj.id" :title="prj.title"></project-item>
    </ul>
    <h3 v-else>No projects found.</h3>
  </base-container>
  <base-container v-else>
    <h3>No user selected.</h3>
  </base-container>
</template>

<script>
import ProjectItem from './ProjectItem.vue';

export default {
  components: {
    ProjectItem,
  },
  props: ['user'],
  data() {
    return {
      enteredSearchTerm: '',
      activeSearchTerm: '',
    };
  },
  computed: {
    hasProjects() {
      return this.user.projects && this.availableProjects.length > 0;
    },
    availableProjects() {
      if (this.activeSearchTerm) {
        return this.user.projects.filter((prj) =>
          prj.title.includes(this.activeSearchTerm)
        );
      }
      return this.user.projects;
    },
  },
  methods: {
    updateSearch(val) {
      this.enteredSearchTerm = val;
    },
  },
  watch: {
    enteredSearchTerm(val) {
      setTimeout(() => {
        if (val === this.enteredSearchTerm) {
          this.activeSearchTerm = val;
        }
      }, 300);
    },
    user() {
      this.enteredSearchTerm = '';
    },
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

### data

```js
data() {
  return {
    enteredSearchTerm: '',
    activeSearchTerm: '',
  };
},

setup() {
  const enteredSearchTerm = ref('');
  const activeSearchTerm = ref('');
},
```

### computed

```js
setup(props, ) {
  const enteredSearchTerm = ref('');
  const activeSearchTerm = ref('');

  const availableProjects = computed(function() {
    if (activeSearchTerm.value) {
      return props.user.projects.filter((prj) =>
          prj.title.includes(activeSearchTerm.value)
      );
    }
    return props.user.projects;
  })
},

availableProjects() {
  if (this.activeSearchTerm) {
    return this.user.projects.filter((prj) =>
      prj.title.includes(this.activeSearchTerm)
    );
  }
  return this.user.projects;
},
```

```js
hasProjects() {
  return this.user.projects && this.availableProjects.length > 0;
},

const hasProjects = computed(function () {
  return props.user.projects && availableProjects.value.length > 0;
})
```

### method

```js
updateSearch(val) {
  this.enteredSearchTerm = val;
},

function updateSearch(val) {
  enteredSearchTerm.value = val;
};
```

### watch
### watch에서 첫 번째 인자로 전달하는 값은 watch function 내부에서 사용하는 값이 아닌 watch 즉, 감시해야할 대상의 
### 변수를 전달하는 것이다.
```js

enteredSearchTerm(val) {
  setTimeout(() => {
    if (val === this.enteredSearchTerm) {
      this.activeSearchTerm = val;
    }
  }, 300);
},

watch(enteredSearchTerm, function (newValue) {
  setTimeout(() => {
    if (newValue === enteredSearchTerm.value) {
      activeSearchTerm.value = newValue;
    }
  }, 300);
});
```

```js
user() {
  this.enteredSearchTerm = '';
},

watch(props, function () {
  enteredSearchTerm.value = '';
})
```

```
위에 watch 중 user를 주의해야 하는데, user 같은경우 props로 넘어온 값으로 watch의 첫 번째 인자로
props.user를 사용하게 된다면, 이 것은 동작하지 않을 것이다. watch(props.user, function() {})
이유는 맨 처음 prop으로 넘어오는 user의 값은 null이기 때문에 vue에서는 이 값을 watch 할 의미가 없기 때문이다.
대신 ref, 나 reactive한 object를 watch하면 된다. props 또한 reactive object 이다. 
props 안에 정의된 value 들은 not ref, not reacvite object 이다. 따라서 props.user 가 아닌, props를 watch 해야 한다.
```

```
현재는 prop가 user 하나여서 props 전체를 watch해도 괜찮지만 여러가지가 생길경우 문제가 발생 할 수 있다. toRefs func을 이용하여
props의 property값들을 ref로 변경하여 해당 ref object를 감시하게 만들 수 있다.
```

```js
// const propsWithRefs = toRef(props);
// const user = propsWithRefs.user;

const { user } = toRefs(props);

watch(user, function () {
  enteredSearchTerm.value = '';
})
```

```vue
//ProjectList.vue

<template>
  <base-container v-if="user">
    <h2>{{ user.fullName }}: Projects</h2>
    <base-search v-if="hasProjects" @search="updateSearch" :search-term="enteredSearchTerm"></base-search>
    <ul v-if="hasProjects">
      <project-item v-for="prj in availableProjects" :key="prj.id" :title="prj.title"></project-item>
    </ul>
    <h3 v-else>No projects found.</h3>
  </base-container>
  <base-container v-else>
    <h3>No user selected.</h3>
  </base-container>
</template>

<script>
import { ref, computed, watch, toRefs } from 'vue';
import ProjectItem from './ProjectItem.vue';

export default {
  components: {
    ProjectItem,
  },
  props: ['user'],
  setup(props, ) {
    const enteredSearchTerm = ref('');
    const activeSearchTerm = ref('');

    const availableProjects = computed(function() {
      if (activeSearchTerm.value) {
        return props.user.projects.filter((prj) =>
            prj.title.includes(activeSearchTerm.value)
        );
      }
      return props.user.projects;
    })

    const hasProjects = computed(function () {
      return props.user.projects && availableProjects.value.length > 0;
    })

    watch(enteredSearchTerm, function (newValue) {
      setTimeout(() => {
        if (newValue === enteredSearchTerm.value) {
          activeSearchTerm.value = newValue;
        }
      }, 300);
    });

    // const propsWithRefs = toRef(props);
    // const user = propsWithRefs.user;

    const { user } = toRefs(props);

    watch(user, function () {
      enteredSearchTerm.value = '';
    })


    function updateSearch(val) {
      enteredSearchTerm.value = val;
    }

    return {
      enteredSearchTerm,
      activeSearchTerm,
      availableProjects,
      hasProjects,
      updateSearch,
    }
  },
  // data() {
  //   return {
  //     enteredSearchTerm: '',
  //     activeSearchTerm: '',
  //   };
  // },
  computed: {
    // hasProjects() {
    //   return this.user.projects && this.availableProjects.length > 0;
    // },
    // availableProjects() {
    //   if (this.activeSearchTerm) {
    //     return this.user.projects.filter((prj) =>
    //       prj.title.includes(this.activeSearchTerm)
    //     );
    //   }
    //   return this.user.projects;
    // },
  },
  methods: {
    // updateSearch(val) {
    //   this.enteredSearchTerm = val;
    // },
  },
  watch: {
    // enteredSearchTerm(val) {
    //   setTimeout(() => {
    //     if (val === this.enteredSearchTerm) {
    //       this.activeSearchTerm = val;
    //     }
    //   }, 300);
    // },
    // user() {
    //   this.enteredSearchTerm = '';
    // },
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
props로 전달받은 값을 바로 template에 사용한다면 따로 정의하지 않아도 된다.
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880346#content
