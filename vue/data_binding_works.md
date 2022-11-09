# 데이터 바인딩 작동원리

### 아래와 같이 method를 사용하는 것은 좋지않다.[outputNullname()]
```xml
<button v-on:click="counter++">Add</button>
<button v-on:click="counter--">Remove</button>
<p>Result: {{ counter }}</p>

<p>Your name: {{ outputNullname() }}</p>
```
```javascript
data() {
    return {
      counter: 10,
      confirmedName: '',
      name:  '',
    };
  },

methods: {
    outpuFullname() {
      if (name === '') {
        return '';
      }
    },
}
```

```
페이지 내용의 변경이 일어날때마다 해당 메소드를 매번 실행하게 된다. 이유는
vue에서는 해당 메소드가 무엇을 하는지 알 수 없기 때문이다(logic이 어떠한지, 무엇을 return 하는지)
따라서 이벤트에 바인딩되지 않은 메소드는 (v-on) 화면에서 어떠한 데이터가 변경되면 vue에서 다시 실행된다.

1. counter를 증가시키는 button을 클릭한다.
2. vue는 다시 렌더링을하게 되는데, 여기서 vue 에서는 outputFullname() 의 메소드가 무엇을 하는지 모른다.
3. vue는 메소드를 다시 실행하게 된다. even though not use the data of counter that be changed 
4. 우리는 해당 메소드가 변경된 counter data를 사용하지 않음을 알지만, vue는 그렇지 않다.

성능관점에서 볼 때 그리 좋지않다. 
```

### 더 나은 방법
```
해당 method logic를 html 코드에 넣는 방법
```

```xml
<p>Your name: {{ name + ' ' + 'last name' }}</p>
```

```
vue 보간에서 무엇이 사용되는지 알게 되기 때문에, 위와같은 상황처럼 [method 매번호출] 되지않는다. [name 사용을 알기 때문에]
그러나, 해당 방식에서는 html 코드에 많은 logic을갖게된다. 이것보다 그렇게 좋지않다.
```

#### computed 사용하기
```
computed는 method와 거의동일하게 동작한다. computed는 종속성을 갖고있는[computed가 갖고있는] 데이터에 대해 해당  
데이터에 변경이 일어나면 다시 호출된다. vue의 computed property naming은 data와 같이 명사형 이름을 지어주는것이 좋다.[It's up to you]
computed를 html에서 사용할때는 ()를 통해 호출하지않는다. 단지 pointing 하고, vue가 호출할 것이다. 단지 data를 사용하는 것 처럼 말이다.
data처럼 사용하기에 data와 같은 명사로 이름을 지어주는 것이 좋다.
computed는 method와 같이 counter가 변경되어도 call 되지 않는다.
(처음 렌더링될때 해당 computed를 vue 가 call 한다. 계산된 값을 얻기 위해서..)
vue는 computed value를 caching 하다가, computed가 갖고있는 data가 변경되면 다시 계산하여 caching 한다.[여기서는 name]
```

```html
<p>Your name: {{ fullname }}</p>
```

```javascript
computed: {
    fullname() {
       console.log("method called");
      if (this.name === '') {
        return '';
      }
      return this.name + "last name";
    }
  },
```
