# building transition with js

```
css로 transition을 control 했었지만 css 이외에도 js로도 transition의 event를 만들 수 있다.
```

```vue
//App.vue

<template>
  <div class="container">
    <div class="block" :class='{animate: animatedBlock}'></div>
    <button @click='animateBlock'>Animate</button>
  </div>
  <div class='container'>
    <transition :cass="false"
                @before-enter='beforeEnter'
                @enter="enter"
                @after-enter='afterEnter'
                @before-leave='beforeLeave'
                @leave='leave'
                @after-leave='afterLeave'
                @enter-cancelled='enterCancelled'
                @leave-cancelled='leaveCancelled'>
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
    enterCancelled(el) {
      clearInterval(this.enterInterval);
      console.log(el);
    },
    leaveCancelled(el) {
      clearInterval(this.leaveInterval);
      console.log(el);
    },
    beforeEnter(el) {
      console.log('beforeEnter()');
      console.log(el);

      el.style.opacity = 0;
    },
    enter(el, done) {
      console.log("enter()");
      console.log(el);
      let round = 1;
      this.enterInterval = setInterval(() => {
        el.style.opacity = round * 0.01;
        round++;
        if (round > 100) {
          clearInterval(this.enterInterval);
          done();
        }
      }, 20);
    },
    afterEnter(el) {
      console.log("afterEnter");
      console.log(el);
    },
    beforeLeave(el) {
      console.log('beforeLeave()');
      console.log(el);

      el.style.opacity = 1;
    },
    leave(el, done) {
      console.log("leave");
      console.log(el);

      let round = 1;
      this.leaveinterval = setInterval(() => {
        el.style.opacity = 1 - round * 0.01;
        round++;
        if (round > 100) {
          clearInterval(this.leaveinterval);
          done();
        }
      }, 20);
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
      enterInterval: null,
      leaveInterval: null,
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

```
각각의 transition hook에 method를 추가하여 dom을 조작하여 처리한다.
beforeEnter 와 beforeLeave는 animation 시작 전 단계이고, enter와 leave에서 각각의 animation을 처리한다.
동시에 2개의 이벤트가 발생하면 [ enter, leave ] 이벤트가 끝나지 않았기에 2개의 이벤트로 인해 animation이 겹치게 된다
이러한 것을 방지하기위해 enterCancelled 과 leaveCancelled 이벤트를 추가한다.
enterCancelled는 enter event의 취소이고, leaveCancelled는 leave event의 취소이다.
```

```
transiton의 element에 [ :cass="false" ]를 추가하면, css로 사용하지않고 js로 사용한다고 vue에게 알려주는 것이다.
css 클래스에 관련 검색을 건너뛰고 js만 실행하게 되기에 약간의 성능이 증가한다.
```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
