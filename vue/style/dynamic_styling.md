# vue_style  [스타일 바인딩]

```xml
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Vue Basics</title>
    <link
      href="https://fonts.googleapis.com/css2?family=Jost:wght@400;700&display=swap"
      rel="stylesheet"
    />
<!--    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>-->
    <link rel="stylesheet" href="styles.css" />
    <script src="https://unpkg.com/vue@next" defer></script>
    <script src="app.js" defer></script>
  </head>
  <body>
    <header>
      <h1>Vue Dynamic Styling</h1>
    </header>
    <section id="styling">
      <div class="demo" v-bind:style="{'border-color' : 'red'}" @click="boxSelected('A')"></div> <!-- 1 -->
      <div class="demo" @click="boxSelected('B')"></div>
      <div class="demo" @click="boxSelected('C')"></div>
    </section>
  </body>
</html>

```
```
1. v-bind를 통해 style을 binding 하는데 해당 더블 쿼테이션에 들어가는 것은 자바스크립트 객체이다.
해당 객체에는 대쉬[-]를 허용하지 않기에 싱글 쿼테이션을 제거하고, 
카멜케이스로 작성을 권장한다. ["{borderColor: 'red'}"] ([-]로도 사용가능하다 위에 element 참고)
또한 해당 데이터를 vue 객체의 data와 같이 사용할 수 있다.
ex) 해당 엘레먼트가 선택이 되었다면, red로 아니라면 gray로 표시한다
["{borderColor: boxASelected ? 'red' : '#ccc'}"]
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
    }
})
app.mount('#styling')
```
