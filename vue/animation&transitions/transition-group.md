# transition_group
```
transition 을 사용할 때는 transition으로 감싼 element는 하나만 렌더링 되어야 했었다. [ v-if 사용가능 v-else와 함께 둘 중 하나만 렌더링 되었기 때문에]
어떠한 상황에서는 하나 이상의 element에 대해 애니메이션이 필요하기 때문에 그러한 문제를 해결하고자 vue 에서는
transition-group이라는 tag는 제공한다.
```

```vue
//UserList.vue

<template>
  <ul>
    <transition-group>
      <li v-for='user in users' :key='user' @click='removeUser(user)'>
        {{user}}
      </li>
    </transition-group>
  </ul>
  <div>
    <input type='text' ref='userNameInput'/>
    <button @click='addUser'>Add User</button>
  </div>
</template>

<script>
export default {
  name: 'ListData',
  data() {
    return {
      users: ['Max', 'Manu', 'Julie', 'Angela', 'Michael'],
    }
  },
  methods: {
    removeUser(user) {
      this.users = this.users.filter(usr => usr !== user);
    },
    addUser() {
      const enteredUserName = this.$refs.userNameInput.value;
      this.users.unshift(enteredUserName);
    },
  }
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 1rem 0;
  padding: 0;
}

li {
  border: 1px solid #ccc;
  padding: 1rem;
  text-align: center;
}
</style>
```

```
transition-group 아래에 있는 div는 input을 통해 user를 추가할 수 있는 영역이다.
transition-group은 transition과 다르게 dom에 렌더링된다. [ tansition tag는 dom에 렌더링되지 않는다. ]
따라서 transition-group의 element가 어떠한 element로 렌더링될 것인지에 대해 정의하기 위해 transition-group element에
tag 속성을 통해 렌더링될 element를 정의한다. [ 정의 시 custom element도 사용 가능하다. ]
```

```vue
//UserList.vue

<template>
    <transition-group tag='ul' name='user-list'> //
      <li v-for='user in users' :key='user' @click='removeUser(user)'>
        {{user}}
      </li>
    </transition-group>
  <div>
    <input type='text' ref='userNameInput'/>
    <button @click='addUser'>Add User</button>
  </div>
</template>
```

```
또한 transition에서 사용했던 name 속성을 transition-group에서도 동일하게 사용 가능하다.
name 속성은 enter-from, enter-leave와 같이 prefix의 값을 담당한다.
```

```vue
//UserList.vue

<template>
    <transition-group tag='ul' name='user-list'>
      <li v-for='user in users' :key='user' @click='removeUser(user)'>
        {{user}}
      </li>
    </transition-group>
  <div>
    <input type='text' ref='userNameInput'/>
    <button @click='addUser'>Add User</button>
  </div>
</template>

<script>
export default {
  name: 'ListData',
  data() {
    return {
      users: ['Max', 'Manu', 'Julie', 'Angela', 'Michael'],
    }
  },
  methods: {
    removeUser(user) {
      this.users = this.users.filter(usr => usr !== user);
    },
    addUser() {
      const enteredUserName = this.$refs.userNameInput.value;
      this.users.unshift(enteredUserName);
    },
  }
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 1rem 0;
  padding: 0;
}

li {
  border: 1px solid #ccc;
  padding: 1rem;
  text-align: center;
}

.user-list-enter-from {
  opacity: 0;
  transform: translateX(-30px);
}
.user-list-enter-active{
  transition: all 1s ease-out;
}
.user-list-enter-to,
.user-list-leave-from{
  opacity: 1;
  transform: translateX(0);
}

.user-list-leave-active{
  transition: all 1s ease-in;
}
.user-list-leave-to{
  opacity: 0;
  transform: translateX(30px);
}
</style>
```

```
위에서 살펴볼 수 있듯이 현재 위에서 적용한 것은 한 element에 대한 animation만을 제공하고 있다. element가 add될 때도 다른 
element에 대해 animation이 적용되어야 한다. 이러한 animation을 vue에서는 css 값으로 user-list [transition-group-name]-move 라는 class를 제공한다.
```

```vue
//UserList.vue

...

<style scoped>
ul {
  list-style: none;
  margin: 1rem 0;
  padding: 0;
}

li {
  border: 1px solid #ccc;
  padding: 1rem;
  text-align: center;
}

.user-list-enter-from {
  opacity: 0;
  transform: translateX(-30px);
}
.user-list-enter-active{
  transition: all 1s ease-out;
}
.user-list-enter-to,
.user-list-leave-from{
  opacity: 1;
  transform: translateX(0);
}

.user-list-leave-active{
  transition: all 1s ease-in;
}
.user-list-leave-to{
  opacity: 0;
  transform: translateX(30px);
}

.user-list-move {
  transition: transform 0.8s ease;
}
</style>
```

```
user-list-move 에서 transition: transform 을 사용한 이유는 vue 내부에서 animation의 대상자가 아닌 다른 element에 대해
내부적으로 element들의 new place로 transform css property를 사용하여 자동으로 이동하기 때문이다.
하지만 여전히 스냅 동작을 가지고 있다. [ 스냅 동작이란 animation이 끝나고 난 후 끊키듯이 animation이 동작한다는 것..? ]
이러한 것을 해결하기 위해 user-list-leave-active css property에 [ position: absoulte ]를 추가한다.
```

```css

<style scoped>
ul {
  list-style: none;
  margin: 1rem 0;
  padding: 0;
}

li {
  border: 1px solid #ccc;
  padding: 1rem;
  text-align: center;
}

.user-list-enter-from {
  opacity: 0;
  transform: translateX(-30px);
}
.user-list-enter-active{
  transition: all 1s ease-out;
}
.user-list-enter-to,
.user-list-leave-from{
  opacity: 1;
  transform: translateX(0);
}

.user-list-leave-active{
  transition: all 1s ease-in;
  position: absolute; //
}
.user-list-leave-to{
  opacity: 0;
  transform: translateX(30px);
}

.user-list-move {
  transition: transform 0.8s ease;
}
</style>
```



##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
