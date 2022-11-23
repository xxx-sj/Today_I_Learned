# vue 반응성

```
일반적인 javascript는 기본적으로 반응적이지 않다.
``` 

```javascript
let message = "Hello";

let longMessage = message + "World!"; //Hello World!
console.log(longMessage) // Hello World!

message = "Hello!!!!!"; 
console.log(longMessage) // Hello World!
```

```
이처럼 message가 변경되어도 longMessage의 값은 변하지 않는다.
```

```
vue는 message가 change 될 때 message에 의존하는 앱의 일부가 업데이트 될 수 있다. [ 프록시를 사용하여 ]
```

##### 출처 : https://www.udemy.com/course/vuejs-2-the-complete-guide
