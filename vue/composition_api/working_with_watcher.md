# working with watchers

```
watch속성도 이 전 computed와 비슷하다. vue에서 watch를 import 받아 선언하여 사용한다.
```

```js

 watch(uAge, (newValue, oldValue) => {
      console.log("old age:" + oldValue);
      console.log("new Age:" + newValue);
    });
```

```
watch function의 첫 번째 인자로는 감시할 변수를 넘기고, 두 번째 인자로는 감시할 변수의 update가 일어날 때 실행할 method를 넘긴다.
이 전 option api에서 사용하던 watch와 비교하면 첫 번째 인자는 watch의 method들 중 func name에 해당한다 생각하면 쉽다.
```

```
options api와는 다르게 watch에서 여러가지의 변수를 감시할 수 있다.
```

```js
watch([uAge, uName], (newValues, oldValues) => {
      console.log("old age:" + oldValues[0]);
      console.log("new Age:" + newValues[0]);
      console.log("old name:" + oldValues[1]);
      console.log("new name:" + newValues[1]);
    });
```

```
이 처럼 composition api에서는 watch의 첫 번째 인자로 watch하고 싶은 변수들을 배열로 넘기면 function에서 배열로 newVal과 oldVal를 인자로 받는다.
newVal과 oldVal 역시 배열로 값을 전달 받게 된다.
```

###### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880256#content
