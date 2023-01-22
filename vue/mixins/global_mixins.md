# global mixins

글로벌 믹스인은 모든 component에 영향을 끼친다. mixins을 정의한 js 파일을 main.js 파일에 import하여 app instance에 mixins을 추가한다.

```js
import { createApp } from 'vue';

import App from './App.vue';
import loggerMixing from "@/mixins/logger";

const app = createApp(App)

app.mixin(loggerMixing);

app.mount('#app');
```

```js
export default {
    mounted() {
        console.log('Mounted!');
    }
}
```

이번 mixin에서는 life cycle hook을 정의했는데, life-cycle-hook 같은 경우 component내에 선언한 메소드가 실행된 이 후 mixin에 정의한 
life-cycle-hook method가 실행된다. data와 같이 덮어지지 않는다.

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880376#content
