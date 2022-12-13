# transition event

```
transition의 event hook으로 몇 가지 존재한다.
- before-enter
- enter
- after-enter
- before-leave
- leave
- after-leave

enter와 leave는 각각의 active에 상응한다.
after-enter와 after-leave는 active의 이벤트가 모두 끝난 후에 호출된다.
```

```vue
//App.vue

<template>
  <div class="container">
    <div class="block" :class='{animate: animatedBlock}'></div>
    <button @click='animateBlock'>Animate</button>
  </div>
  <div class='container'>
    <transition name='para'
                @before-enter='beforeEnter'
                @enter="enter"
                @after-enter='afterEnter'
                @before-leave='beforeLeave'
                @leave='leave'
                @after-leave='afterLeave'
    >
        <p v-if='paraIsVisible'>This is only sometimes visible...</p>
    </transition>
    <button @click='toggleParagraph'>Toggle Paragraph</button>
  </div>
  <div class='container'>
    <transition name='fade-button' mode='out-in'>
      <button @click='showUsers' v-if='!usersAreVisible'>Show Users</button>
      <button @click='hideUsers' v-else>Hide Users</button>
    </transition>
  </div>
  <base-modal @close="hideDialog" :open='dialogIsVisible'>
    <p>This is a test dialog!</p>
    <button @click="hideDialog">Close it!</button>
  </base-modal>
  <div class="container">
    <button @click="showDialog">Show Dialog</button>
  </div>
</template>

<script>
export default {
  methods: {
    beforeEnter(el) {
      console.log('beforeEnter()');
      console.log(el);
    },
    enter(el) {
      console.log("enter()");
      console.log(el);
    },
    afterEnter(el) {
      console.log("afterEnter");
      console.log(el);
    },
    beforeLeave(el) {
      console.log('beforeLeave()');
      console.log(el);
    },
    leave(el) {
      console.log("leave");
      console.log(el);
    },
    afterLeave(el) {
      console.log("afterLeave");
      console.log(el);
    },
    showUsers() {
      this.usersAreVisible = true;
    },
    hideUsers() {
      this.usersAreVisible = false;
    },
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
  data() {
    return {
      animatedBlock: false,
      dialogIsVisible: false,
      paraIsVisible: false,
      usersAreVisible: false,
    };
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

.para-enter-from {
  /*opacity: 0;*/
  /*transform: translateY(-30px);*/
}

.para-enter-active {
  /*transition: all 0.3s ease-out;*/
  /*animation: slide-scale 0.3s ease-out;*/
  animation: slide-scale 2s ease-out;
}

.para-enter-to {
  /*opacity: 1;*/
  /*transform: translateY(0);*/
}

.para-leave-from {
  /*opacity: 1;*/
  /*transform: translateY(0);*/
}
.para-leave-active {
  /*transition: all 0.3s ease-in;*/
  animation: slide-scale 0.3s ease-in;
}
.para-leave-to {
  /*opacity: 0;*/
  /*transform: translateY(30px);*/
}

.fade-button-enter-from,
.fade-button-leave-to{
  opacity: 0;
}

.fade-button-enter-active {
  transition: opacity 0.3s ease-out;
}
.fade-button-leave-active {
  transition: opacity 0.3s ease-in;
}

.fade-button-enter-to,
.fade-button-leave-from{
  opacity: 1;
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
</style>
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
