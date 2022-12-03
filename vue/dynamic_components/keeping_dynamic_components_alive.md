#  dynamic components keeping alive

```
component를 통해 동적으로 component를 바꾸면 만약, A component에 input tag가 있고 해당 태그에 데이터를 입력 후
B태그로 전환하고 다시 A태그로 돌아오게 되면 이 전에 입력한 데이터는 날아가고 없다. 이유는 component를 통해 동적으로
컴포넌트를 바꾸게 되면 존재하던 component는 파괴되어 DOM에서 제거 되기 때문이다. 다시 돌아왔을 때는 새로운 component가 
생기는 것이다.
```

```
이러한 상황을 방지하고자 vue에서 제공하는 태그가 있다. <keep-alive> 라는 태그이다. keep-alive 태그는 component 태그와
함께 동작한다. keep-alive 태그는 vue에게 구성요소를 완전히 제거하거나 삭제해서는 안되고, 상태를 저장해야 한다고 알린다.
```

```vue
//App.vue

<template>
  <div>
    <the-header></the-header>
    <button @click="setSelectedComponent('active-goals')">Active goals</button>
    <button @click="setSelectedComponent('manage-goals')">Manage goals</button>
<!--    <active-goals v-if="selectedComponents === 'active-goals'"></active-goals>-->
<!--    <manage-goals v-if="selectedComponents === 'manage-goals'"></manage-goals>-->
    <keep-alive>
      <component :is="selectedComponents"></component>
    </keep-alive>
  </div>
</template>

<script>
import TheHeader from '@/components/TheHeader'
import ActiveGoals from "@/components/ActiveGoals";
import ManageGoals from "@/components/ManageGoals";

export default {
  components: {
    TheHeader,
    ActiveGoals,
    ManageGoals,
  },
  data() {
    return {
      selectedComponents: 'active-goals',
      activeUser: {
        name: 'Maximilian Schwarzmüller',
        description: 'Site owner and admin',
        role: 'admin',
      },
    };
  },
  methods: {
    setSelectedComponent(cmp) {
      this.selectedComponents = cmp;
    }
  }
};
</script>
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide
