# 생명주기

```
app.mount를 통해 vue가 마운트 될 위치를 알 수 있도록 전달한다. 
이후 app 생성이 시작되면 몇가지 단계에 도달한다.
해당 메소드들은 vue를 생성할 때 만든 configuration object에 추가할 수 있다.
처음으로 도달하게 되는 lifecycle hook은 beforeCreate()이다.
```

```
createApp({....})
      |
      |
beforeCreate()
      |
      |
  created()
```

```
beforeCreate는 기본적으로 앱이 완전히 초기화되기 전에 호출되며,
create는 그 이후에 호출된다. 이 두 시점에서는 화면에 아무것도 표시가 되지 않는다.
created 이후에 vue는 단지 data properties만 알고 있으며, 일반적인 app configuration을 알고 있다.
```

```
createApp({....})
      |
      |
beforeCreate()
      |
      |
  created()
      |
      |
[Compile template]
```

```
compile template 에서는 모든 dynamic placeholders, the interpolations 등등.. 들이 제거되며, concrete value로 대체된다.
```

```
```
createApp({....})
      |
      |
beforeCreate()
      |
      |
  created()
      |
      |
[Compile template]
      |
      |
  beforeMount()
```

```
이 후 beforeMount에 도달하게 된다. 이때는 vue가 실제로 screen에 무언가를 가져온다. 우리가 screen에서 무언가를 보기 바로 
전 상태이다. 
```

```
createApp({....})
      |
      |
beforeCreate()
      |
      |
  created()
      |
      |
[Compile template]
      |
      |
  beforeMount()
      |
      |
    Mounted()
```

```
mounted에 도달하게 되면 우리는 화면에서 렌더링된 것을 볼 수 있다. vue는 초기화 되었으며, template은 comile 되었으며, 
vue는 화면에 무엇을 보여줄 지 알고있으며, 그리고 browser에 넘기면서 브라우저는 실제로 모든 html element와 우리가 필요로 하는
content를 추가한다. [vue instance 에 정의한 바와 같이.]
```


```
createApp({....})
      |
      |
beforeCreate()
      |
      |
  created()
      |
      |
[Compile template]
      |
      |
  beforeMount()
      |
      |
  Mounted()
      |
      |
Mounted vue instance
      |
      |
  data changed
      |
      |
beforeUpdate()
      |
      |
  updated()
```

```
data가 변경되면 beforeupdate() hook에 도달하게 되고 beforeUpdate() 이후 updated() hook에 도달한다.
updated 이 후에는 mounted에 도달하지 않는다. template가 unmounted 된적이 없기 때문이다.
```

```
createApp({....})
      |
      |
beforeCreate()
      |
      |
  created()
      |
      |
[Compile template]
      |
      |
  beforeMount()
      |
      |
  Mounted()
      |
      |
Mounted vue instance
      |
      |
  data changed
      |
      |
beforeUpdate()
      |
      |
  updated()
      |
      |
instance Unmounted
      |
      |
beforeUnmount()
      |
      |
  unmounted()
```

```
instance를 unmounted 시점에 beforeUnmount와 unmounted hook에 도달하게 된다. 해당 instance가 제거되기 전에 
beforeUnmount 가 호출되며 제거 이후 unmounted가 호출된다. 이 hook에서는 clean up 할 code가 있다면 여기서 작성한다.
앱이 unmounted 되는것을 추적하기 위한 서버에 http 요청 보내기 등.. 

```
##### 출처 : https://www.udemy.com/course/vuejs-2-the-complete-guide
