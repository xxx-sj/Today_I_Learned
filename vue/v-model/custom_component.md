# custom component에서 v-model 사용하기 [ 양방향 바인딩 ]

```
vue2 에서는 양방향 바인딩을 위해 .sync[ parent에서 사용 ] 키워드와 this.$emit("update:propName", "data for update") 처럼 사용하였다.
```

```vue
//parent

<template>
  <child :props-date.sync="parentPropsdata"/>
</template>

<script>
export default {
  data() {
    return{
      prentPropData: "parent data",
    };
  }
}

</script>
```

```vue
//child

<template>
  <button @click="updateProp">on event</button>
<template>

<script>
export default {
  props: ['propsData'],
  data() {
    return {};
  },
  methods: {
    updateProp() {
      this.$emit("update:propsData", "child data");
    }
  },
}
</script>
```

```
vue3에서는 
custom component에서 v-model을 사용하는 것은 prop으로 data를 넘기면서 [ :prop-data="propData" ] 
이벤트를 수신하는 것과 같다. [ @update:modelValue=""] (update:modelValue 가 emit으로 넘기는 event name 이다.) 
[vue3 특정인지 확인해 봐야할 듯 하다.]
```

```vue
//TheForm.vue - parent

...
<div class='form-control'>
    <rating-control v-model='rating' :model-value='rating'/> //
</div>
...

<script>
import RatingControl from '@/components/RatingControl';
export default {
  components: { RatingControl },
  data() {
    return {
      userName: '',
      userAge: null,
      referrer: 'wom',
      interest: [],
      how: null,
      confirm: false,
      rating: null, //
      userNameValidity: 'pending',
    };
  },
  
 methods: {
    submitForm() {
      console.log('UserName: ' + this.userName);
      this.userName = '';
      console.log("userAge: ");
      console.log(this.userAge);
      console.log(31);
      this.userAge = null;
      console.log("Referrer: " + this.referrer);
      this.referrer = "wom";
      console.log("checkboxes");
      console.log(this.interest);
      console.log("raido buttons");
      console.log(this.how);
      this.interest = null;
      this.how = null;
      console.log("rating");
      console.log(this.rating);
      this.rating = null;
    },

    validateInput() {
      if (this.userName.trim() === '') {
        this.userNameValidity = 'invalid';
      } else {
        this.userNameValidity = 'valid';
      }
    },
  },
}
</script>
```


```vue
//RatingControl - child

<template>
  <ul>
    <li :class='{active: activeOption === "poor"}'><button type='button' @click='activate("poor")'>Poor</button></li>
    <li :class='{active: activeOption === "average"}'><button type='button' @click='activate("average")'>Average</button></li>
    <li :class='{active: activeOption === "great"}'><button type='button' @click='activate("great")'>Great</button></li>
  </ul>
</template>

<script>
export default {
  props: ['modelValue'], //
  emits: ['update:modelValue'], //
  name: 'RatingControl',
  data() {
    return {
      activeOption: this.modelValue, // prop으로 받은 data를 초기화할 때 사용한다.
    };
  },

  methods: {
    activate(option) {
      this.activeOption = option;
      this.$emit("update:modelValue", option); //
    }
  }
};
</script>
```


```
parent에서 submit이 후 rating을 null로 초기화하는데 [ custom component와 v-model로 binding한 값 ]
이 후 rating값은 null로 바뀌지만, child-component에는 전달되지 않아 UI가 rerendering되지 않는다.
이 것을 수정하기 위해서 2가지 방법이 있는데, 첫 번째로는 child-component dat에서 현재 prop으로 넘겨받은
modelValue를 activeOption[local data] 초기화 할 때만 사용하고 있어서 update가 되지 않는 것인데 이것을
compouted 속성을 이용해 변경하는 것이다.
```

```vue
//ratingControl -child

<template>
  <ul>
    <li :class='{active: activeOption === "poor"}'><button type='button' @click='activate("poor")'>Poor</button></li>
    <li :class='{active: activeOption === "average"}'><button type='button' @click='activate("average")'>Average</button></li>
    <li :class='{active: activeOption === "great"}'><button type='button' @click='activate("great")'>Great</button></li>
  </ul>
</template>

<script>
export default {
  props: ['modelValue'],
  emits: ['update:modelValue'],
  name: 'RatingControl',

  computed: {
    activeOption() {
      return this.modelValue;
    }
  },

  methods: {
    activate(option) {
      // this.activeOption = option;
      this.$emit("update:modelValue", option);
    }
  }
};
</script>
```

```
button을 클릭해서 activate 메소드가 호출되면 emit을 통해 modelValue의 값을 변경하게 되고,
parent에서 submit을 하면 rating 값이 null로 초기화되면서 다시 prop을 통해 modelValue의 값이
null로 변경된다. 그 변경을 computed는 감지하여 값을 초기화 하게 되는 것이다.
```

```
두 번째로는 button class의 조건을 activeOption에서 modelValue로 변경하는 것이다. 
여기서는 button event로 option값을 인자로 넘겨 emit으로 값을 변경하고 있기 때문에 문제가 없다.
```

```vue
//child

<template>
  <ul>
    <li :class='{active: modelValue === "poor"}'><button type='button' @click='activate("poor")'>Poor</button></li>
    <li :class='{active: modelValue === "average"}'><button type='button' @click='activate("average")'>Average</button></li>
    <li :class='{active: modelValue === "great"}'><button type='button' @click='activate("great")'>Great</button></li>
  </ul>
</template>

<script>
export default {
  props: ['modelValue'],
  emits: ['update:modelValue'],
  name: 'RatingControl',
  // data() {
  //   return {
  //     activeOption: this.modelValue,
  //   };
  // },

  // computed: {
  //   activeOption() {
  //     return this.modelValue;
  //   }
  // },

  methods: {
    activate(option) {
      // this.activeOption = option;
      this.$emit("update:modelValue", option);
    }
  }
};
</script>
```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
