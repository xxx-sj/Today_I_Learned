
```
component는 vue의 기능이다. 우리가 생성하는 vue instacne 에 만들 수 있다.
첫 번째로 중요한 요소는 식별자이다. [ 식별자는 custom html tag와 비슷하다.]
식별자를 지을 때는 dash를 포함하는 것이 좋다. 잠재적으로 충돌하는 것을 방지할 수 있기 때문이다.
두 번째로 인자로 전달하는 값은 config object 다.
vue component는 본질적으로 또 다른 vue 앱이기 때문이다. 다른 앱에 속한 다른 app.
대신, main app에 연결된 app 인 것이다.
```

```javascript
const app = ...


app.component('friend-contact', {
    data() {
        return {
            detailsAreVisible: false
        };
    },
    methods: {
        toggleDetails() {
            this.detailsAreVisible = !this.detailsAreVisible;
        }
    }
});
```

```
parent app에도 detailsAreVisible 을 갖고 있지만 해당 component는 상위 앱과 지금은 통신하고 있지 않기 때문에, 같은 값을
가지고 있어도 된다. [ 캡슐화 ]
```

```
다음으로 중요한 것은 component도 새로운 앱이므로 template가 필요하다. [ mount 할 부분. parent app에서는 section 부분에 해당.]
그렇다고 해서 app 처럼 mount를 call하지 않는다. 대신 새로만든 app에 template 속성을 사용한다. 
```

```html
<section id="app">
  <ul>
    <friend-contact />
  </ul>
</section>
```

```
app.component('friend-contact', {
    template: `
        <li>
          <h2>{{ friend.name }}</h2>
          <button @click="toggleDetails(friend.id)">{{ detailsAreVisible ? 'hide' : 'show' }} Details</button>
          <ul v-if="detailsAreVisible">
            <li><strong>Phone:</strong> {{ friend.phone }}</li>
            <li><strong>Email:</strong> {{ friend.email }}</li>
          </ul>
        </li>`,
    data() {
        return {
            detailsAreVisible: false
        };
    },
    methods: {
        toggleDetails() {
            this.detailsAreVisible = !this.detailsAreVisible;
        }
    }
});
```

```
해당 html element는 브라우저가 알지 못하기 때문에 vue가 mount한 부분에서만 동작한다. 벗어날경우 브라우저가 무시하고 
렌더링하지 않는다. vue는 해당 custom tags를 detected 하면, 해당 component에 정의한 template를 렌더링한다.
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide
