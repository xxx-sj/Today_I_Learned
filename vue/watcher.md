# watcher [감시자]

``` 
watcher는 설정한 값이 변경되면 call되는 function이다. computed와 비슷하지만 사용법이 다르다.
data or computed property를 사용하여 watch의 method를 정의하는데, vue는 data가 변경되었을 때 해당 watch method   
를 실행한다. 
```

```javascript
data() {
    return {
      counter: 10,
      confirmedName: '',
      name:  '',
      fullname: '',
    };
  },
  
  
  watch: {
    name() {}
  },
```

```
해당 method내에는 logic을 작성할 수 있다.
```

```javascript
 name() {
      this.fullname =  " "+ " " + "last name";
    }
```

```
watcher function은 vue에 의해 전달된 인수를 받게되는데, 해당 인수는 data의 최신 값이다.
[마지막으로 update된 name의 value]
```

```javscript
watch: {
    name(value) {
      this.fullname = value + " " + "last name";
    }
  },
//만약, name 과 binding 된 input이 있다면[v-model="name"], 해당 input을 통해 name이 update될 것이고,
//watch method를 통해 fullname이 update될 것이다. 여기서, value의 값으로 매번 update된 name이 전달된다.
```
