# 동적 컴포넌트

```
2개의 컴포넌트가 있다. 어떠한 액션 or 이벤트에 의해 하나의 component가 화면에 렌더링되야 한다면,
v-if를 통해 해당 component를 렌더링 할지 말지를 결정할 수 있다.
```

```vue
//App.vue

<template>
  <div>
    <the-header></the-header>
    <button @click="setSelectedComponent('active-goals')">Active goals</button> //어떠한 이벤트
    <button @click="setSelectedComponent('manage-goals')">Manage goals</button> //어떠한 이벤트
    <active-goals v-if="selectedComponents === 'active-goals'"></active-goals> //v-if조건으로 렌더링 결정
    <manage-goals v-if="selectedComponents === 'manage-goals'"></manage-goals> //v-if조건으로 렌더링 결정
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
      selectedComponents: 'active-goals', //추가된 컴포넌트
      activeUser: {
        name: 'Maximilian Schwarzmüller',
        description: 'Site owner and admin',
        role: 'admin',
      },
    };
  },
  methods: {
    setSelectedComponent(cmp) { //이벤트
      this.selectedComponents = cmp;
    }
  }
};
</script>
```

```
계속해서 component가 증가한다면, button 및 custom comonent, 그리고 v-if가 반복적으로 발생하게 된다.
vue에서는 이러한 기능을 제공하는데, component element 이다.
component 태그 [vue에서 제공하는]는 독립적으로 동작하지 않고, [<component /> 만 있어서는 동작하거나 렌더링되지 않는다.]
is라는 property를 추가해줘야 한다. [ <component is="" />] is property는 component 태그에 어떠한 component를 렌더링 해야 
하는지 알려주는 역할을 한다. [<component is="active-goals"/>] is의 값으로 component값을 넘겨주면 된다.
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
    <component :is="selectedComponents"></component>
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


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/
