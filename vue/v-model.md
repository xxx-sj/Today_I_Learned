# 양방햔 바인딩  [v-model]
```
input 이벤트를 기다리면서 동시에 값을 다시 쓴다.
```
```
[v-model] 은 v-bind:value 와 v-input 을 합친것과 같다.
input에 text를 입력하면 v-input이벤트가 동작하며, v-bind에 연결된 data의 값이 update된다.
```

```javascript
//태그 영역
<input type="text" v-input="setName" v-bind="name">

//vue data
 name:  '',
 
 //method
setName(event) {
      this.name = event.target.value
    },
    
========
<input type="text" v-model="name" />
```
