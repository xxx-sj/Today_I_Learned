#

![image](https://user-images.githubusercontent.com/62305110/204092275-d7acd96c-00bb-4b90-baea-98ea820a59e3.png)


- package.json
```
우리가 실행할 수 있는 스크립트나, 종속성을 정의한 곳이다.
dependencies는 해당 packages를 코드에서 사용할 수 있으며, 자동으로 코드에 import된다.
html 파일에  script tags 를 추가할 필요가 없다.
devDependencies
또한 package 이지만, 해당 프로젝트 내에서만 set up.
```

- node_modules
```
package.json 에 정의한 package들을 다운로드하여 모아둔 곳. [ npm install ] 
```

-public
```
public 폴더 내부에 index.html 파일이 있는데, 우리의 application이 mount될 파일이다.
```html
<!DOCTYPE html>
<html lang="">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
    <title><%= htmlWebpackPlugin.options.title %></title>
  </head>
  <body>
    <noscript>
      <strong>We're sorry but <%= htmlWebpackPlugin.options.title %> doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```

-src (source)
```
해당 폴더는 main working directory 이다. [ 작성할 js 파일이 src 내부에 있다. ]
- main.js
  해당 앱의 기본 진입점이다. [뷰 프로젝트는 화면 뒤에서 main.js 파일을 가장 먼저 loads 하고 runs 한다.]
  vue 에서 createApp fun을 import 하여 해당 app을 mount 시킨다.
  [ Vue.createApp() -> createApp() ]
  그리고 나서 설정한 app.vue를 mount 시킨다.
- App.vue
  .vue 파일은 vue.js의 특별한 기능이다. 또는 vue cli 의 기능이라고 말할 수 있다.
  vue파일에는 3가지 section으로 나뉜다. [ html code (app or a component), script, style ]
  
  html
  html코드에는 template tag가 있는데, 해당 tag는 앱의 template를 포함하고 있다.
  이전에 js 파일에 문자열로 작성한 template 파일과 같은 부분이다.
  
  script
  실제로 config object를 포함하고 있는 부분이다.
  이 부분에서 볼 수 있는것이 export default 인데, 이전에는 Vue.createApp({}) 인자로 vue config object를 넘겼었는데,
  그 대신 해당 script 부분이 export되면서 대체 되었다.
  [ Vue.createApp({}) -> createApp(App) ]
  
  style
  해당 appd에서 사용할 스타일을 정의하는 곳이다.
```

```javascript
//main.js
import { createApp } from 'vue'
import App from './App.vue'

createApp(App).mount('#app')
```

```vue
<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <HelloWorld msg="Welcome to Your Vue.js App"/>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  components: {
    HelloWorld
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

- src/components
```
해당 프로젝트의 components를 모아두는 폴더이다.
```


```
vue cli로 만든 프로젝트는 build step이라는 transform 을 거쳐서 브라우저에서 동작할 수 있는 코드로 변경한다.
[ 표준 js code로 변경하는 것 ]
```

##### 출처 : https://www.udemy.com/course/vuejs-2-the-complete-guide
