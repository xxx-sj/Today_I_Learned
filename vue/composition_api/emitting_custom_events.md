# emitting custom events

```
vue 에 의해 setup 메소드의 인자로 전달받는 2번 째 값은 context이다. context에는 [ attr, emit, slots, expose ]등이 있다.
```
![image](https://user-images.githubusercontent.com/62305110/212522233-47b0eb94-ddf4-4e3e-804e-d91806890bd9.png)

### 컴포넌트에서 props와 emits으로 정의해둔 게 아닌 속성이나 이벤트 리스너는 fallthrough attribute라 합니다.
### fallthrough attribute는 props나 emits으로 전달받지 않고 컴포넌트에 전달되는 속성, 이벤트 리스너들입니다.
### prop으로 정의하지 않은 속성은 전달받은 child component의 최상위 태그의 속성이 된다.
### tag event도 동일한데, 만약 child component의 최상위 tag가 button이고 child button에 click 리스너를 정의하고
### parent에도 child component에 click 리스너를 정의하였다면, 먼저 child component의 리스너를 처리 후 parent에서 정의한
### 리스너를 처리하게 된다.

#### attrs

```vue
//parent

<base-button @click="parent"/>

//child

<button @click="child" />


순서: child -> parent
```

```
context의 첫 번째로 attr가 있는데, attr은 fallthrough attribute로, child component에 props으로 정의한 값이 아니다.
[ 만약 child에 class라는 prop을 정의했다면 child component props에는 props: ['class']와 같이 정의되어 있었을 것이다.
parent에서 user-data의 component에 class 속성을 정의하게 되면 child component에서 context.attr에 접근하여 확인해보면
class의 값이 들어있는 것을 확인할 수 있다.
```

```vue
//App.vue parent/

<template>
  <section class="container">
    <user-data :first-name="firstName" :last-name="lastName" :age="age"
    class="test"></user-data>                                                 //
    <button @click="setAge">Change Age</button>
    <div>
      <input type="text" placeholder="First Name" v-model="firstName"/>
      <input type="text" placeholder="Last Name" ref="lastNameInput"/>
      <button @click="setLastName">Set Last Name</button>
    </div>
  </section>
</template>
```

```vue
//UserData.vue child/

<template>
  <div>
    <h2> {{ userName }}</h2>
    <h3> {{ age }}</h3>
  </div>
</template>

<script>
import {computed} from 'vue'
export default {
  name: "UserData",
  props: ['firstName', 'lastName', 'age'],
  setup(props, context) {
    const uName = computed(function() {
      return props.firstName + '' + props.lastName;
    });

    console.log(context)                                    //

    return { userName: uName };
  },
  // computed: {
  //   userName() {
  //     return this.firstName + " " + this.lastName
  //   }
  // },
}
</script>
```
![image](https://user-images.githubusercontent.com/62305110/212522805-6b24fcf2-538c-4a0a-bef3-9b1fccbd62d9.png)

```
child component의 root tag가 여러개라면 어디에 binding해야 하는지 모르기 때문에 warn이 발생한다.
```
```html
//UserData.vue

<template>
    <h2> {{ userName }}</h2>
    <h3> {{ age }}</h3>
</template>
```
![image](https://user-images.githubusercontent.com/62305110/212522824-da502730-f02d-4825-8859-e5170eb1bb83.png)


#### slots

```
context의 두 번째로는 slots이다. slots는 component에 정의된 slot data를 보여준다.
```

#### emit
```
option api에서는 this.$emit('emitFuncName', args...); 처럼 child에서 parent로 custom event를 전달했었다. 하지만
composition api에는 this keyword를 사용할 수 없다. 대신 context를 사용하여 나타낼 수 있다.
```

```js
context.emit('save-data', 1); // ===  this.$emit('save-data', 1);
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880214#content
https://www.vuelog.dev/post/vue/[Vue-3]-Composition-API---context-(attrs-slots-emit-expose)#context
