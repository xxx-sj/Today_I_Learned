# what is composition api 

```
우리는 지금까지 소위 options api라고 부르는 것을 사용하여 vue apps / components를 building 해왔다.
우리는 이 configuration object를 만들어서 그 안에 configured various options  했다.
예를들어 data options, method options, computed, watcher 등등.. 
options api로 만드는것은 분명히 좋고 고집해도 된다. 그렇게다면 composition api란 무엇인가?
```

### composition api
```
composition api는 대안적인 방법이다. might face two main limitations / issues when building bigger vue apps.
작은 앱에서는 이슈를 발견하지 못할수도 있습니다.
각각의 컴포넌트들은 data, methods, computed 등을 가지고 있다. 만약 한 컴포넌트 내에 seach에 관련된 data가 존재하고
그에 따른 computed, method , watch 등이 있다면, data를 수정하게 된다면 이들을 모두 수정해야 할 수도 있다.
re-using locic across components can be tricky or cumbersome 하다.
```

```
이전 options api 처럼 사용하는 것들을
{
  data() {
    return {
      name: "s"
    }
  },
  methods: {
    dosomething() {...}
  }
}
```

```
새로운 setup method로 번들 한다.
{
  setup() {
    const name = ref('s');
    funtion dosomething() {...}
    return { name, dosomething } 
  }
}
setup메소드안에 이전에 적용했던 options들을 사용한다.
```

``` 
main option인 data, methods, computed, watch를 merge 하여 setup() 메소드에 넣는다.
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21527260#content
