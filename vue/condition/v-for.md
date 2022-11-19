# 반복문

```
javascript에서 사용하는 for문과 유사하게 vue에서도 for문[반복문]을 제공한다.
```

```html
<li v-for="goal in goals">Goal {{ goal }}</li>
```

```
범위는 태그와 태그 내부로 한정된다.
v-for는 dom을 전체 update하는 것이 아니라 dom 에 element를 추가하는 방식으로 동작한다.
```

```javascript
data() {
    return {
      enteredGoalValue: '',
      goals: [],
    };
  },
```

```
v-for에서 index 도 가져올 수 있다 [ v-for="(goal, index) in goals" ]
```

```
v-for 반복문은 배열 이외에도 객체를 반복해서 출력할 수 있다.
```
```html
<li v-for="value in {name: 'max', age: '31'}">{{ value }}</li> 
// max
// 31
```
```
또한 index를 가져왔듯이, 객체에서 key값을 가져올 수 있다
```
```html
<li v-for="(value, key) in {name: 'max', age: '31'}">{{key}} : {{ value }}</li> 
//name : max
//age: 31
```
```
추가적으로, index 또한 가져올 수 있다.
```

```html
<li v-for="(value, key, index) in {name: 'max', age: '31'}">{{key}} : {{ value }} - {{ index }}</li> 
//name : max - 0
//age: 31 - 1
```

```
v-for에서도 해당 data를 사용할 수 있다. element를 클릭하면 해당 data를 삭제하는 메소드를 작성한다.
```

```html
<li v-for="(goal, index) in goals" @click="removeGoal(index)">{{ goal }}</li>
```

```
const app = Vue.createApp({
  data() {
    return {
      enteredGoalValue: '',
      goals: [],
    };
  },

  methods: {
    addGoal() {
      this.goals.push(this.enteredGoalValue);
    },
    removeGoal(idx) {
      this.goals.splice(idx, 1);
    },
  }
});

app.mount('#user-goals');
```

```html
<ul v-else>
<li v-for="(goal, index) in goals" @click="removeGoal(index)">
  {{ goal }}
  <input  type="text" @click.stop />
</li>
</ul>
```

```
input tag를 클릭했을 때 .stop을 사용하지 않는다면, 이벤트 전파에의해 li태그의 click 메소드인 
removeGoal이 동작하게 된다.
```

### v-for에서 렌더링 주의점

```
아래와 같은 태그 형태에 v-for를 써야할 때 주의할 점이 있다. 
[v-for를사용하며, 해당 태그 내부에 input태그를 사용하고, 태크 클릭 시 해당 태그를 삭제할 때]

시나리오
1. input을 통해 li태그를 2개를 생성하게 된다.
1-1. 해당 li태그에는 input 태그가 존재하는데, 첫 번째 input 태그에 text를 입력하고, 2 번째 태그에는 입력하지 않는다..
2. 이 후, 입력된 첫 번째 태그를 삭제하면, 첫 번째 태그에 입력한 text가 두 번째 input태그에 들어가게된다.
2-1 이는 첫 번째 li가 삭제되면서 두 번째 li가 올라오게 되면서 발생한다.

시나리오
1. 위와 같이 하되, 이번엔 첫 번째 input에는 아무것도 입력하지 않고, 두 번째 input 태그에 text를 입력한다.
2. 이 후 첫 번째 태그를 삭제하면 두 번째 태그가 올라오면서 아무것도 입력되지 않은 상태가 된다. [input 태그 value가 비어있게 됨]
```

### 문제점

```
vue는 항목을 추가하고 제거할 때 목록을 업데이트한다. 필요에 따라 실제 dom에서 목록을 렌더링한다. 이는 성능을 최적화하기 위함인데,
vue는 dom 요소를 재사용한다는 의미이다.
두 개의 li dom element가 있다고 가정하자. 만약 첫 번째 li를 삭제하면 vue는 실제 전체 li list를 다시 렌더링하지않는다. 또, 첫 번째
li도 삭제하지 않는다. 대신 기본적으로 두 번째 li의 content를 가지고 첫 번째 dom 요소로 이동시킨다.
```

```html
<ul v-else>
<li v-for="(goal, index) in goals" @click="removeGoal(index)">
  {{ goal }}
  <input  type="text" @click.stop />
</li>
</ul>
```

```
두 개의 li태그가 있고 내부에는 input 태그가 있다 가정하자. 첫 번째 li 태그에는 input태그에 text가 없고, 2 번째 li 태그 내부 input
에는 text를 입력한 상태이다. 이 상태에서 첫 번째 태그를 삭제하면, 두 번째에 입력한 text가 삭제된다.
이는 vue에서 성능을 위해 dom 요소를 재활용하기 때문인데, 첫 번째 요소를 삭제할 때 해당 요소를 삭제하는 것이 아닌, 2 번째에 있는 content들을 
1 번째 요소에 옮긴다.. [여기서 말하는 content는 {{}} 보간법 등을 사용한 데이터 들이다. [동적 데이터] {{index, value, key, etc...}} ]
이 때, input 태크도 재활용하게 되는데 요소가 삭제되지않아 첫 번째 input을 사용하게되면서 두 번째 input에 입력한 text가 날아가게 되는 것이다.
```

### 해결점
```
이는 모두 동일한 요소를 가지고 있기에 발생하는 문제인데 [유니크한(고유한) 식별 기준이 없기 때문에 렌더링되는 dom 요소에 대해], 
이를 해결하기 위해 key라는 attribute를 [ v-for를 사용하는 요소에 ] 추가하면 된다. (key는 기본 html attribute가 아니다.)

```

```html
<ul v-else>
<li v-for="(goal, index) in goals" v-bind:key="index" @click="removeGoal(index)">
  {{ goal }}
  <input  type="text" @click.stop />
</li>
</ul>
```


```
index를 쓰면 좋을 것이라 생각할 수 있다[index는 모두 갖고 있기 때문에] 하지만, 2 개의 요소가 있다고 한다면
첫 번째 요소를 삭제하면[index= 0] 두 번째 요소가 index -> 1 를 갖는 것이 아닌, index ->0 을 갖게되므로 같은 상황이 된다.
이는 해당 요소의 content가 유니크한 값을 갖지 않기 때문이다 [삭제하게 되면 index가 동적으로 변한다. index는 content에 속하지[net belong to] 않는다.]
[index는 항상 동일하다.] 따라서 인덱스는 좋지 못하다. 2번째 후보로는 goal[입력한 text]을 들 수 있다. 같은 text를 입력하게 된다면 index와
별반 다르지 않다. 보다 현실적인 방법은 id 이다. [database에 저장된 고유 id (unique key)]
```

```html
<ul v-else>
    <li v-for="(goal, index) in goals" :key="goal" @click="removeGoal(index)">
  {{ goal }}
  <input  type="text" @click.stop />
</li>
</ul>
```

```
key값으로 vue에게 다른 태그라는것을 알려줌으로써  vue는 두 번째 태그에 attached된 content를 잃지않기 위해
첫 번째 element를 삭제하게 된다.
```
##### 출처 : https://www.udemy.com/course/vuejs-2-the-complete-guide
