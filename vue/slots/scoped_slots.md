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
      <slot v-bind:item="goal" another-prop="..." v-bind:test="oneData"></slot>
    </li>
  </ul>
</template>

<script>
export default {
  name: "CourseGoals",
  data() {
    return {
      goals: ['finish the course', 'learn vue'],
      oneData: [],
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
전달받은 data를 사용하기 위해서는 v-slot:default="[prop-object-name]" 처럼 사용해야 하는데 해당 object는 slot에서 전달하는
모든 데이터의 object이다. [ <template v-slot:default="slotProps"></template> ] 
가령, slot에서 전달하는 데이터를 2개를 정의했다면, [ <slot v-bind:item="goal" another-prop="..." ></slot> ]
2개의 데이터는 slotProps object 에 모두 포함되어 있다.
이 후 사용은 동일하게 해당 범위 내에서 정의한 데이터[slotProps]를 사용할 수 있다
slot에서 정의한 prop data가 - [dash] 를 포함한 경우, [another-prop] vue는 자동으로 카멜케이스로 변환하지 않기 때문에,
slotProps를 사용하는 component에서는 . [dot] 이 아닌 []로 접근해야 한다. [ slotProps['another-prop'] ]
```
### slotProp - [dash]에 대하여
```
현재 듣고있는 강의에서는 slot의 prop로 dash를 사용할 경우 사용하는 쪽에서도 dash로 정의한 property 그대로 사용해야 한다 하지만
[another-prop] 
현재 2022-12-03 기준
vue-cli: @vue/cli 5.0.8
dash로 정의하던, 카멜케이스로 정의하던 vue가 자동으로 카멜케이스로 인식하도록 변경하는 듯 하다. 현재로는 dash로는 사용 불가능 하다.
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
        <template #default="slotProps">
        <h2> {{ slotProps.item }} </h2>
        <h3> {{ typeof slotProps.test }}</h3>
        <p> {{slotProps['anotherProp'] }} </p>
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

```
만약 정의된 slot이 기본slot 하나만 있다면 template 태그를 없애고, 정의한 template component에 직접 작성할 수 있다.
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
    <course-goals #default="slotProps">
      <h2> {{ slotProps.item }} </h2>
      <h3> {{ typeof slotProps.test }}</h3>
      <p> {{ slotProps.anotherProp }} </p>
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
