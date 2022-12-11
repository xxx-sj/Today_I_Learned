# transition tag

```
css 속성인 animation으로는 dialog 같은 경우 dom에서 사라지는 것에 대한 animation을 부여할 수 없다. 
vue에서 이것을 도와주기 위한 component를 제공하는데 그것이 transition 이다.
animation을 구현해야 하는 부분을 transition component로 감싼다. 단, transition은 하나의 child component만 감싸야 한다.
```

```
transition은 해당 요소에 몇가지 css 유틸리티 클래스를 추가한다.
- *-enter-from
- *-enter-active
- *-enter-to
enter-from class를 먼저 추가하고, 동시에 enter-active 클래스를 추가한다.
그리고 enter-from이 제거된 후 [enter-active는 유지 ] animation이 끝나면 enter-to가 추가된다.

이후 element가 unmounted 되면 그 사이 위와 같이 leave-from class와 leave-active class가 추가되고
이후 leave-from class가 사라지면서 leave-to class가 추가된다.

leave-form + leave-active + leave-to 이후 element는 dom에서 실제로 삭제된다.
```


```
.v-enter-from {
  opacity: 0;
  transform: translateY(-30px);
}
.v-enter-active {
  transition: all 0.3s ease-out;
}
.v-enter-to {
  opacity: 1;
  transform: translateY(0);
}

위 3개의 css class는 위에서 말했던 것처럼 요소가 dom에 처음 추가될 때 추가된다.
v-enter-from 은 element의 시작 애니메이션, v-enter-to는 애니메이션의 마지막
v-enter-active는 중간에서 animation의 duration이나, 감시해야하는 animation의 종류를 정의한다.
```

```vue
//App.vue


<template>
  <div class="container">
    <div class="block" :class='{animate: animatedBlock}'></div>
    <button @click='animateBlock'>Animate</button>
  </div>
  <div class='container'>
    <transition>
        <p v-if='paraIsVisible'>This is only sometimes visible...</p>
    </transition>
    <button @click='toggleParagraph'>Toggle Paragraph</button>
  </div>
  <base-modal @close="hideDialog" v-if="dialogIsVisible">
    <p>This is a test dialog!</p>
    <button @click="hideDialog">Close it!</button>
  </base-modal>
  <div class="container">
    <button @click="showDialog">Show Dialog</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      animatedBlock: false,
      dialogIsVisible: false,
      paraIsVisible: false,
    };
  },
  methods: {
    animateBlock() {
      this.animatedBlock = true;
    },
    toggleParagraph() {
      this.paraIsVisible = !this.paraIsVisible;
    },
    showDialog() {
      this.dialogIsVisible = true;
    },
    hideDialog() {
      this.dialogIsVisible = false;
    },
  },
};
</script>

<style>
* {
  box-sizing: border-box;
}
html {
  font-family: sans-serif;
}
body {
  margin: 0;
}
button {
  font: inherit;
  padding: 0.5rem 2rem;
  border: 1px solid #810032;
  border-radius: 30px;
  background-color: #810032;
  color: white;
  cursor: pointer;
}
button:hover,
button:active {
  background-color: #a80b48;
  border-color: #a80b48;
}
.block {
  width: 8rem;
  height: 8rem;
  background-color: #290033;
  margin-bottom: 2rem;
  /*transition: transform 0.3s ease-out;*/

}
.container {
  max-width: 40rem;
  margin: 2rem auto;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  padding: 2rem;
  border: 2px solid #ccc;
  border-radius: 12px;
}
.animate {
  /*transform: translateX(-150px);*/
  animation: slide-fade 0.3s ease-out forwards;
}

.v-enter-from {
  opacity: 0;
  transform: translateY(-30px);
}

.v-enter-active {
  transition: all 0.3s ease-out;
}

.v-enter-to {
  opacity: 1;
  transform: translateY(0);
}

.v-leave-from {
  opacity: 1;
  transform: translateY(0);
}
.v-leave-active {
  transition: all 0.3s ease-in;
}
.v-leave-to {
  opacity: 0;
  transform: translateY(30px);
}

@keyframes slide-fade {
  0% {
    transform: translateX(0) scale(1);
  }

  70% {
    transform: translateX(-120px) scale(1.1);
  }

  100% {
    transform: translateX(-150px) scale(1);
  }
}
</style>
```


```
to와 from을 쓰지않고 active만 사용해서 animation 구현이 가능하다.
```

```css
.v-enter-from {
  /*opacity: 0;*/
  /*transform: translateY(-30px);*/
}

.v-enter-active {
  /*transition: all 0.3s ease-out;*/
  animation: slide-scale 0.3s ease-out;
}

.v-enter-to {
  /*opacity: 1;*/
  /*transform: translateY(0);*/
}

.v-leave-from {
  /*opacity: 1;*/
  /*transform: translateY(0);*/
}
.v-leave-active {
  /*transition: all 0.3s ease-in;*/
  animation: slide-scale 0.3s ease-in;
}
.v-leave-to {
  /*opacity: 0;*/
  /*transform: translateY(30px);*/
}

@keyframes slide-scale {
  0% {
    transform: translateX(0) scale(1);
  }

  70% {
    transform: translateX(-120px) scale(1.1);
  }

  100% {
    transform: translateX(-150px) scale(1);
  }
}
```

```
animation이 끝나면 모든 class는 사라진다.
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
