# using_with_custom_component

```
custom component에도 동일하게 transition을 사용할 수 있다. 
```

```vue
//App.vue 

<transition name='modal'>
  <base-modal @close="hideDialog" v-if="dialogIsVisible">
    <p>This is a test dialog!</p>
    <button @click="hideDialog">Close it!</button>
  </base-modal>
</transition>

.modal-enter-active {
  animation: modal 0.3s ease-out;
}

@keyframes modal {
  from {
    opacity: 0;
    transform: translateY(-50px) scale(0.9);
  }

  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}
```

```vue
//BaseModal.vue

<template>
  <div class="backdrop" @click="$emit('close')"></div>
  <dialog open>
    <slot></slot>
  </dialog>
</template>

<script>
export default {
  emits: ['close'],
};
</script>

<style scoped>
.backdrop {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100vh;
  z-index: 10;
  background-color: rgba(0, 0, 0, 0.75);
}

dialog {
  position: fixed;
  top: 30vh;
  width: 30rem;
  left: calc(50% - 15rem);
  margin: 0;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  border-radius: 12px;
  padding: 1rem;
  background-color: white;
  z-index: 100;
  border: none;
  /*animation: modal 0.3s ease-out forwards;*/
}
```

```
여기서 문제가 발생하는데 baseModal을 보면 root element가 2개이다 transition은 root element가
1개여야 사용이 가능하다.
```

```
첫 번째 해결방법은 animation이 들어가야하는 component에 transition을 설정하는 방법이다. 
정확히는 baseModal에 추가하는 것이 아닌, BaseModal 내부에 dialog에 transition을 추가하는 것이다.
```

```
여기서 문제가 하나 더 있는데 기존에 App.vue에서 BaseModal을 v-if 를 통해 렌더링을 할지 말지를 결정했었다.
이럴 경우 transition이 baseModal 내부에 포함되어있어 [ dialog를 감싸고 있음. ]v-if로 인해 동작하지 않는다.
이 문제를 해결하기 위해서 dialog의 option인 open을 이용하여 App.vue에서 button을 클릭하면 showDialog method를 통해
dialogIsVisible data를 true / false로 toggle 하기 때문에 이 data값을 prop을 통해 전달하여 BaseModal이 받아 처리한다.
```


```vue
//App.vue

<template>
  <div class="container">
    <div class="block" :class='{animate: animatedBlock}'></div>
    <button @click='animateBlock'>Animate</button>
  </div>
  <div class='container'>
    <transition name='para'>
        <p v-if='paraIsVisible'>This is only sometimes visible...</p>
    </transition>
    <button @click='toggleParagraph'>Toggle Paragraph</button>
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

.para-enter-from {
  /*opacity: 0;*/
  /*transform: translateY(-30px);*/
}

.para-enter-active {
  /*transition: all 0.3s ease-out;*/
  animation: slide-scale 0.3s ease-out;
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

/*@keyframes modal {*/
/*  from {*/
/*    opacity: 0;*/
/*    transform: translateY(-50px) scale(0.9);*/
/*  }*/

/*  to {*/
/*    opacity: 1;*/
/*    transform: translateY(0) scale(1);*/
/*  }*/
/*}*/

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

```vue
//BaseModal.vue


<template>
  <div v-if='open' class="backdrop" @click="$emit('close')"></div>
  <transition name='modal'>
    <dialog open v-if='open'>
      <slot></slot>
    </dialog>
  </transition>
</template>

<script>
export default {
  props: ['open'],
  emits: ['close'],
};
</script>

<style scoped>
.backdrop {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100vh;
  z-index: 10;
  background-color: rgba(0, 0, 0, 0.75);
}

dialog {
  position: fixed;
  top: 30vh;
  width: 30rem;
  left: calc(50% - 15rem);
  margin: 0;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  border-radius: 12px;
  padding: 1rem;
  background-color: white;
  z-index: 100;
  border: none;
  /*animation: modal 0.3s ease-out forwards;*/
}

.modal-enter-active {
  animation: modal 0.3s ease-out;
}

.modal-leave-active {
  animation: modal 0.3s ease-in reverse;
}

@keyframes modal {
  from {
    opacity: 0;
    transform: translateY(-50px) scale(0.9);
  }

  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}
</style>
```

```
dialog의 open 속성을 v-bind를 통해 하려 하지만 이 행동은 동작하지 않는다.
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
