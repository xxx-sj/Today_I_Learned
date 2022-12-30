# adding route page components

![image](https://user-images.githubusercontent.com/62305110/210078535-d15071ff-22c2-4fb0-88f3-982e59a631f8.png)

```
main.js 에서 router 사용하기
```

```js
//main.js

import { createApp } from 'vue';

import router from '@/router';
import App from '@/App';

const app = createApp(App)

app.use(router)
app.mount("#app");
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21879890#content
