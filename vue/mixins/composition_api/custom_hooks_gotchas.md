# custom hooks gotchas

이 전에 만들어두었던 search.js 를 이용하여 아래의 component도 다시 정의한다. 

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
import {computed, watch, toRefs } from 'vue';

import ProjectItem from './ProjectItem.vue';
import useSearch from "@/hooks/search";

export default {
  components: {
    ProjectItem,
  },
  props: ['user'],
  setup(props) {
    const projects = props.user ? props.user.projects : [];

    const {enteredSearchTerm, availableItems, updateSearch} = useSearch(
        projects,
        'title');
    // const enteredSearchTerm = ref('');
    // const activeSearchTerm = ref('');
    //
    // const availableProjects = computed(function () {
    //   if (activeSearchTerm.value) {
    //     return props.user.projects.filter((prj) =>
    //       prj.title.includes(activeSearchTerm.value)
    //     );
    //   }
    //   return props.user.projects;
    // });

    const hasProjects = computed(function () {
      return props.user.projects && availableItems.value.length > 0;
    });

    // watch(enteredSearchTerm, function (newValue) {
    //   setTimeout(() => {
    //     if (newValue === enteredSearchTerm.value) {
    //       activeSearchTerm.value = newValue;
    //     }
    //   }, 300);
    // });

    // const propsWithRefs = toRefs(props);
    // const user = propsWithRefs.user;

    const { user } = toRefs(props);

    watch(user, function () {
      enteredSearchTerm.value = '';
    });

    // function updateSearch(val) {
    //   enteredSearchTerm.value = val;
    // }

    return {
      enteredSearchTerm,
      availableProjects: availableItems,
      hasProjects,
      updateSearch,
    };
  },
  // data() {
  //   return {
  //     enteredSearchTerm: '',
  //     activeSearchTerm: '',
  //   };
  // },
  // computed: {
  //   hasProjects() {
  //     return this.user.projects && this.availableProjects.length > 0;
  //   },
  //   availableProjects() {
  //     if (this.activeSearchTerm) {
  //       return this.user.projects.filter((prj) =>
  //         prj.title.includes(this.activeSearchTerm)
  //       );
  //     }
  //     return this.user.projects;
  //   },
  // },
  // methods: {
  //   updateSearch(val) {
  //     this.enteredSearchTerm = val;
  //   },
  // },
  // watch: {
  //   enteredSearchTerm(val) {
  //     setTimeout(() => {
  //       if (val === this.enteredSearchTerm) {
  //         this.activeSearchTerm = val;
  //       }
  //     }, 300);
  //   },
  //   user() {
  //     this.enteredSearchTerm = '';
  //   },
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

setup method block 처음 줄에 projects를 정의했는데, 만약 props 의 user가 없다면 그 이후 useSearch에 넘기는 인자로 null값이 들어가
오류가 발생하는 것을 방지하기 위해 만든것이다. 하지만 문제가 생기는데, projects를 만들어 인자로 넘기게 되면 setup method는 처음 한 번 호출된
이 후 호출되지 않기 때문에 값이 변경되어도 해당 값이 reactive하지 않게 된다. [ user는 몰라도 user 내부 property는 감지하지 못하는 상황 ]

이 문제를 해결하기 좋은 방법은 computed를 사용하여 reactive 하게 만드는 것이다. computed를 사용하게 되면 내부에 값을 감시하다 변경이 일어나게 되면
vue에게 알리게 되고, re-render를 하게된다. 여기서 추가로 props의 변경을 모두 체크하는 것이 아닌 props의 user만을 체크할 수 있도록 아래에 있던 toRfes코드를
위로 올리게된다.

```vue

//ProjectList.vue

<script>
import {computed, watch, toRefs } from 'vue';

import ProjectItem from './ProjectItem.vue';
import useSearch from "@/hooks/search";

export default {
  components: {
    ProjectItem,
  },
  props: ['user'],
  setup(props) {

    const { user } = toRefs(props);

    const projects = computed(function() {
      return user.value ? user.value.projects : [];
    })

    const {enteredSearchTerm, availableItems, updateSearch} = useSearch(
        projects,
        'title');
    // const enteredSearchTerm = ref('');
    // const activeSearchTerm = ref('');
    //
    // const availableProjects = computed(function () {
    //   if (activeSearchTerm.value) {
    //     return props.user.projects.filter((prj) =>
    //       prj.title.includes(activeSearchTerm.value)
    //     );
    //   }
    //   return props.user.projects;
    // });

    const hasProjects = computed(function () {
      return props.user.projects && availableItems.value.length > 0;
    });

    // watch(enteredSearchTerm, function (newValue) {
    //   setTimeout(() => {
    //     if (newValue === enteredSearchTerm.value) {
    //       activeSearchTerm.value = newValue;
    //     }
    //   }, 300);
    // });

    // const propsWithRefs = toRefs(props);
    // const user = propsWithRefs.user;

    watch(user, function () {
      enteredSearchTerm.value = '';
    });

    // function updateSearch(val) {
    //   enteredSearchTerm.value = val;
    // }

    return {
      enteredSearchTerm,
      availableProjects: availableItems,
      hasProjects,
      updateSearch,
    };
  },
  // data() {
  //   return {
  //     enteredSearchTerm: '',
  //     activeSearchTerm: '',
  //   };
  // },
  // computed: {
  //   hasProjects() {
  //     return this.user.projects && this.availableProjects.length > 0;
  //   },
  //   availableProjects() {
  //     if (this.activeSearchTerm) {
  //       return this.user.projects.filter((prj) =>
  //         prj.title.includes(this.activeSearchTerm)
  //       );
  //     }
  //     return this.user.projects;
  //   },
  // },
  // methods: {
  //   updateSearch(val) {
  //     this.enteredSearchTerm = val;
  //   },
  // },
  // watch: {
  //   enteredSearchTerm(val) {
  //     setTimeout(() => {
  //       if (val === this.enteredSearchTerm) {
  //         this.activeSearchTerm = val;
  //       }
  //     }, 300);
  //   },
  //   user() {
  //     this.enteredSearchTerm = '';
  //   },
  // },
};
</script>
```

이제 search.js에서 모두 ref를 받을 수 있도록 코드를 수정한다.

```js
import {computed, ref, watch} from "vue";

export default function useSearch(items, searchProp) {
    const enteredSearchTerm = ref('');
    const activeSearchTerm = ref('');

    const availableItems = computed(function () {
        let filteredItems = [];
        if (activeSearchTerm.value) {
            filteredItems = items.value.filter((item) =>
                item[searchProp].includes(activeSearchTerm.value)
            );
        } else if (items.value) {
            filteredItems = items.value;
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

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880390#content
