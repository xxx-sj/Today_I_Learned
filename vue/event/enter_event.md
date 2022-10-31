```
하나의 태그에 2개의 이벤트를 사용할 수 있다.
```

```javascript
 <input type="text" v-on:keyup="confirmInput" v-on:input="inputEvent">
```

```
특정 키에 대해서만 이벤트를 받고싶다면 뒤에 수정자를 추가하면 된다.
 <input type="text" v-on:keyup="confirmInput" v-on:input.enter="inputEvent">
```
