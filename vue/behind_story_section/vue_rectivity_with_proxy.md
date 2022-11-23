# vue 반응성
```
proxy를 통해 간단한 접근방식을 알아본다. how Vue works under the hood
```

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

### proxy
```
javascript의 proxy 객체를 사용하여 간단히 vue의 동작방식을 확인한다.
proxy는 인자로 래핑할 data object 와, 해당 data object에 대한 handler를 인자로 받는다.
이번에 handler 내부에 set이란 method를 정의하는데 set이란 메소드의 인자로는 target, key, value를 받는다.
```

```javascript
const data = {
  message: "Hello!",
};

const handler = {
  set(target, key, value) {
    console.log(target);
    console.log(key);
    console.log(value);
  }
};

const proxy = new Proxy(data, handler);

proxy.message = 'Hello!!!!';
```

```
이처럼 proxy는 data를 래핑하였기 때문에 data와 마찬가지로 message의 property를 갖는다.

proxy.message = "Hello!!!"; 를 하게 되면, handler의 set이 호출되면서 target에는 해당 data {message: "Hello"}; key => message,
value => "Hello!!!!"; 가 console에 출력된다.
handler의 set은 마치 setter와 같이 동작하며 래핑한 data의 값[ proxy ]이 변경 될 때마다 호출된다. [ proxy.message = some data ...]
```

```
이번에는 message의 값이 변하면 longMessage의 값도 변경되도록 코드를 바꿔본다. 먼저 proxy에 저장할 data에 property를 추가히고,
```

```javascript
const data = {
  message: "Hello!",
  longMessage: "Hello! World!",
};
```

```
handler의 내부 로직을 변경한다. 
[ 위에서 Hello!는 message의 데이터와 같은 의미로 사용한다. ( longMessage : message + 'World!'  ) ]
```

```javascript
const handler = {
  set(target, key, value) {
    if (key === 'message') {
      target.longMessage = value + ' World!';
    }
    target.message = value;
  }
};
```

```
의사코드로 변경해보자면, 
key의 값이 message이면 = proxy.message = ~
래핑한 데이터의 longMessage의 값을 입력한 message + "World!"로 변경한다. = target.longMessage[proxy.longMessage] = value(set한 값) + " World!";
마지막으로 래핑한 data의 message 의 값을 입력한 값으로 set한다. proxy.message = 'Hello!!!!!';
```

```javascript
const data = {
  message: "Hello!",
  longMessage: "Hello! World!",
};

const handler = {
  set(target, key, value) {
    if (key === 'message') {
      target.longMessage = value + ' World!';
    }
    target.message = value;
  }
};

const proxy = new Proxy(data, handler);
console.log(proxy.longMessage) // Hello! World!

proxy.message = 'Hello!!!!';

console.log(proxy.longMessage) // Hello!!!! World!
```


##### 출처 : https://www.udemy.com/course/vuejs-2-the-complete-guide
