# multiple elements

```
이 전에 transition은 한개의 element만 감쌀 수 있다고 했었는데, 예외가 존재한다.
transition이 감싼 element들 중 DOM에 한 개만 렌더링 된다는 것이 보장된다면 여러개의 element 를 사용할 수 있다.
```

```vue

<div class='container'>
  <transition name='fade-button' mode='out-in'>
    <button @click='showUsers' v-if='!usersAreVisible'>Show Users</button>
    <button @click='hideUsers' v-else>Hide Users</button>
  </transition>
</div>

<script>
export default {
  data() {
    return {
      animatedBlock: false,
      dialogIsVisible: false,
      paraIsVisible: false,
      usersAreVisible: false,
    };
  },
  methods: {
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
tag에 추가되어 있는 mode attribute는 [ out-in, in-out ] 두 개의 값을 받는다.
만약 mode를 사용하지 않는다면, button이 사라지는 동시에 생기기 때문에 두 버튼이 보이게 되어 원하는 결과를 얻지 못한다.
mode="out-in" 은 첫 번째로 out animation 이후 in animation이 동작하게 만든다. 반대로 mode="in-out" 은 in 이 먼저, out이 그 다음이다.
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
