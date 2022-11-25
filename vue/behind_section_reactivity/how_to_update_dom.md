# vue가 dom을 update하는 방법

```
vue는 data의 값이 변경되면 해당 단락의 값만 업데이트된다.
vue는 가상 dom이라는 개념으로 이를 수행한다.
``` 

```
vue 인스턴스는 data, computed, mehotds를 저장한다. 그리고 browser dom은 해당 vue에 영향을 받는다.
vue의 template content 가 dom에 렌더링된다. 그리고 vue가 control 하는 렌더링 프로세스 중에 dynamic part 라던가, 
보간 {{}}, binding이 삭제되고 실제 값이 삽인된다. {{ message }} -> test
template가 dom에 렌더링 될 때 @click 등과 같은 event라던가, 보간들은 vue에 의해 삭제된다.
ex) title: "hello~" -> <p>{{ title }} </p> -> <p> hello~ </p>
data가 변경되면, 변경된 값이 반영이 되어야하는데, vue는 모든 element를 re-render해서는 안된다.
title이 변경되면 해당 title의 해당 태그만 update되어야 한다. 그렇다면 vue는 전체 화면을 re-render 하지않고
변경된 부분만 re-render할 수 있을까?
```

```
만약,
이전 dom의 value와 새로운 dom의 value를 비교한 후, 
전체적으로 렌더링된 dom에 어떠한 content가 있는지 보고, vue의 data property에 저장된 값과 비교하여
변경이 있으면 update한다. 라는 것은 매번 변경이 일어날 때마다 해당 dom을 update해야 하기때문에 그렇게 좋지 않다.
```

```
vue는 virtual dom이라는 개념을 사용한다. virtual dom은 실제 dom을 copy한 것이다. 해당 virtual dom은 자바스크립트로 관리되며,
메모리에 존재한다. 해당 값은 이렇게 보일 수 있다.[ {el: 'h2', child: 'hello!'}, ...] 어떠한 html 태그가 필요한지, 그리고 그 태그의 내용이
무엇인지 등..., data가 변경되면 기본적으로 new virtual dom이 생성되며, 새로 만들어진 virtual dom과 이전(old) virtual dom과 비교한다.
비교하여, 달라진게 있다면 해당 값을 실제 dom에 렌더링한다. 실제 렌더링된 dom에서 해당 작업을 하는 것보다, 이러한 virtual dom을 통해 비교하여
update하는 것이 훨씬 효율적이다.

text를 변경하면 vue가 해당 변경 사항을 감지하고 새로운 virtual dom을 생성한다. 이 후 생성한 virtual dom과 이전 virtual dom을 비교하여
다른점 감지한 후 실제 dom을 update한다. 

```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide
