# why hooks

mixins 보다 hooks이 좋은 이유는 큰 어플리케이션에서는 mixins을 사용하게 되면 어디서 정의한 data인지 function 인지를 찾기 어려웠었다.
그에 비해 custom hooks은 사용하려는 값을 return 하고, 사용하고자 하는 component에서는 해당 값을 import하여 변수로 선언하여 사용하였다.

```js
import {ref} from "vue";

export default function useAlert(startingVisibility = false, ) {
    const alertIsVisible = ref(startingVisibility);

    function showAlert() {
        alertIsVisible.value = true;
    }
    function hideAlert() {
        alertIsVisible.value = false;
    }

    return [
        alertIsVisible,
        showAlert,
        hideAlert
    ];
}
```

```js
setup() {
  const alertTitle = ref('Delete User?');
  // const alertArr = useAlert();
  const [ alertIsVisible, showAlert, hideAlert ] = useAlert(true);

  // return {...alertArr};
  return {
    alertIsVisible,
    showAlert,
    hideAlert,
    alertTitle,
  };
},
```
이렇게 변수에 저장하게 되면 어느 custom hooks에서 온 값 인지를 한번에 확인할 수 있다.

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880384#overview
