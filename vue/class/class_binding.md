# class_binding [클래스 바인딩]
[dynamic_style 에 이어서]


```xml
 <div
          :class="boxASelected ? 'demo active' : 'demo'"
           v-on:click="boxSelected('A')"></div>

```

```css
.active {
  border-color: red;
  background-color: salmon;
}
```

```javascript
const app = Vue.createApp({
    data() {
        return {
            boxASelected: false,
            boxBSelected: false,
            boxCSelected: false,
        };
    },

    methods: {
        boxSelected(box) {
            if(box === 'A') {
                this.boxASelected = true;
            } else if (box === 'B') {
                this.boxBSelected = true;
            } else if (box === 'C') {
                this.boxCSelected = true;
            }
        }
    },
})

app.mount('#styling')
```


```
위와같이 xml에 설정을하면 읽는데에 어려움이 있다.
vue js는 특별한 구분을 제공하는데,  class에도 object를 전달할 수 있다 [:class="{}"]
해당 object의 property name은 적용하려는 css class name 이고, 해당 property의 value는 ture / false 이다.
true / false를 통해 해당 property가 추가될지 말지를 결정하게 된다.
[ :class="{demo: true, active: boxASelected}" ]

demo는 항상추가되고, active는 boxASelected의 값에 따라 추가될지, 말지가 결정된다. (boxASelected는 data의 데이터이다.)
여기서, demo는 항상 적용되어야하는 class라면, 동적 class 추가가 아닌, 정적 class 추가로 할 수 있다.
<div
  class="demo"
  :class="{active: boxASelected}" />
```
```xml

```
