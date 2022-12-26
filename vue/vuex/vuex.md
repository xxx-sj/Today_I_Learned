# vuex
```
vuex는 전역 상태를 관리하기 위한 라이브러리이다.
상태는 단순히 data로 번역될 수 있다. [State -> Data ]
State는 local state 와 global state로 나뉩니다.

local state는 하나의 component 에서 관리하며, 한 component에서만 영향이 미칩니다. 또한 prop를 통해 child component 까지
영향을 미칠 수 있다.

global state 전체 앱에 영향을 미칩니다. 다양한 장소에 필요한 데이터를 사용한다. 로그인 했는지 안했는지, 쇼핑 바구니 목록 등..
```

```
vuex 의 기능을 provide와 inject를 통해 비슷한 기능을 구현해 왔는데, provide와 inject의 문제점이 존재한다.
provide inject 같은경우, parent-child 관계에서만 사용할 수 있어 한정적이고, 한 component 내에서 data와 method가 모여있고, 
변경된다는 점입니다. 실제로 필요없음에도 말이다. 어플리케이션이 커짐이 따라 관리하기가 어려워 질 수 있다.
이러한 문제의 최상의 솔루션이 vuex가 될 수 있다.
```

```
why vuex?

Managing app-wide / global state can be difficult

vuex가 state를 갖게 되면서 Fat components 를 막을 수 있고, unpredictable 한 문제를 해소할 수 있고, 
error-prone 를 방지할 수 있다.
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
