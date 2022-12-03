#  scoped_slots

```
범위 슬롯의 개념은 슬롯을 정의한 컴포넌트[child] 내부에서 정의한 데이터[slot-data]를 슬롯을 정의한 컴포넌트를 사용하는 곳[ parent ]
으로 정의한 데이터[slot-data]를 전달하는 것이다. 
```

```
slot을 정의한 컴포넌트에서 slot tag에 prop를 추가하여 전달한다.
```

```vue
//courseGoals.vue [slot 을 정의한 component]

<template>
  <ul>
    <li v-for="goal in goals" :key="goal">
      <slot v-bind:item="goal"></slot>
    </li>
  </ul>
</template>

<script>
export default {
  name: "CourseGoals",
  data() {
    return {
      goals: ['finish the course', 'learn vue'],

    };
  }
}
</script>
```

```
위에서 사용한 item은 v-bind를 통해 전달될 prop의 name이고, goal은 현재 컴포넌트에서 사용중인 data 이다.
```

```
전달한 prop data[item]는 slot component[courseGoals.vue]를 사용을 위해 정의한 곳[App.vue]에서 엑세스해서 사용한다.
전달받은 data를 사용하기 위해서는 v-slot:default=
```

```vue
//App.vue


<template>
  <div>
    <the-header></the-header>
    <badge-list></badge-list>
    <user-info
      :full-name="activeUser.name"
      :info-text="activeUser.description"
      :role="activeUser.role"
    ></user-info>
    <course-goals>
      <template #default> // v-slot:default
        <h2></h2>
      </template>
    </course-goals>
  </div>
</template>

<script>
import TheHeader from './components/TheHeader'
import BadgeList from './components/BadgeList.vue';
import UserInfo from './components/UserInfo.vue';
import CourseGoals from "./components/CourseGoals";

export default {
  components: {
    'the-header': TheHeader,
    'badge-list': BadgeList,
    'user-info': UserInfo,
    'course-goals': CourseGoals,
  },
  data() {
    return {
      activeUser: {
        name: 'Maximilian Schwarzmüller',
        description: 'Site owner and admin',
        role: 'admin',
      },
    };
  },
};
</script>
```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide
