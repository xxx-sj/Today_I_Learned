#  computed를 사용한 클래스 바인딩
[class_dynamic_binding 이어서..]



```
html 코드에 추가된 logic을 옮겨오고 싶다면, 아래와 같이 computed 에 옮겨서 사용할 수 있다.
```

```html
<div
     class="demo"
:class="{active: boxASelected}" // 이부분을 아래와 같이 변경한다.
 v-on:click="boxSelected('A')"></div>
```

```html
<div
  class="demo"
  :class="boxAClasses"
 v-on:click="boxSelected('A')"></div>

```

```javascript
const app = Vue.createApp({
    ...

//이부분
    computed: {
      boxAClasses() {
        return {active: this.boxASelected};
      },
    },

  ...
})

app.mount('#styling')
```

```
computed를 사용하면 좋은 이유는 inline에서 보다 복잡한 logic을 사용할 수 있으며, 
복잡한 check를 해야하는 경우 등에 사용하면 좋다. [if...] 복잡하지 않다면 inline도 좋다.
```
