# v-if [조건식]

```
vue 에서 javascript처럼 조건식을 사용할 수 있다
[v-if="조건식" ( true / false ) true / false를 나타내는 값이라면 모두 사용가능하다. (computed 사용가능) ]
다른 if문 처럼 else if 와 else를 사용 할 수 있다.
v-else를 쓸 때 주의해야 할 사항이 있는데, v-else는 v-if 다음으로 와야한다. 만약, v-if를 사용한 태그와 
v-else를 사용한 태그 사이데 다른 태그가 있다면 오류가 발생한다. v-else-if는 사이에 사용 가능

<v-if ... />
<p />
<v-else... /> //error
```

```html
<p v-if="">No goals have been added yet - please start adding some!</p>
```

```
예시로, data에 선언한 list 객체가 비어있다면, <p>태크를 비어있지않고 data가 있다면 list를 보여준다.
```

```html
<p v-if="goals.length === 0">No goals have been added yet - please start adding some!</p>
 <ul v-else>
  <li>Goal</li>
</ul>
```

```javascript
const app = Vue.createApp({
  data() {
    return { goals: ['finish the course'] };
  },
});

app.mount('#user-goals');
```

```
렌더링이 될 때 v-if표현식으로 작성한 태그는 조건에 부합하지않는다면, 렌더링되지 않는다.
```

```
이번엔 동적으로 input tag에 입력한 text를 list data인 [goals]에 저장하여 리스트를 보여줄 것이다.
```

```html
<section id="user-goals">
    <input type="text" />
    <button>Add Goal</button>
    <p v-if="goals.length === 0">No goals have been added yet - please start adding some!</p>
    <ul v-else>
      <li>Goal</li>
    </ul>
</section>
```

```javscript
const app = Vue.createApp({
  data() {
    return {
      goals: ['finish the course']
    };
  },
});

app.mount('#user-goals');
```

```
이제 input과 연결한 data와 입력 후 버튼을 눌렀을 때 input 에 입력한 text를 goals 에 저장하는 메소드를 만든다.
```

```javascript
const app = Vue.createApp({
  data() {
    return {
      enteredGoalValue: '',
      goals: ['finish the course']
    };
  },

  methods: {
    addGoal() {
      this.goals.push(this.enteredGoalValue);
    },
  }
});

app.mount('#user-goals');
```

```
새로 작성한 data와 method를 html에 바인딩한다 [v-model, v-on:click]
```

```html
<section id="user-goals">
  <h2>My course goals</h2>
  <input type="text" v-model="enteredGoalValue"/>
  <button @click="addGoal">Add Goal</button>
  <p v-if="goals.length === 0">No goals have been added yet - please start adding some!</p>
  <ul>
    <li>Goal</li>
  </ul>
</section>
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide
