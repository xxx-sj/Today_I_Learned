# component 들어가기에 앞서

```html
<section id="app">
  <ul>
    <li>
      <h2>Manuel Lorenz</h2>
      <button>Show Details</button>
      <ul>
        <li><strong>Phone:</strong> 01234 5678 991</li>
        <li><strong>Email:</strong> manuel@localhost.com</li>
      </ul>
    </li>
    <li>
      <h2>Julie Jones</h2>
      <button>Show Details</button>
      <ul>
        <li><strong>Phone:</strong> 09876 543 221</li>
        <li><strong>Email:</strong> julie@localhost.com</li>
      </ul>
    </li>
  </ul>
</section>
```

```javavscript
const app = Vue.createApp({
    data() {
       return {};
    },
   
});

app.mount("#app");
```

```
위와 같이 반복되는 부분이 있는 코드가 있다면, 우리는 data에 해당 값들을 선언하고 [ db조회를 통해 server에서 받아온 ]
v-for를 이용할 것이다.
```

```html
<section id="app">
  <ul>
    <li v-for="friend in friends" :key="friend.id">
      <h2>{{ friend.name }}</h2>
      <!-- -->
      <button @click="toggleDetails">{{ detailsAreVisible ? 'hide' : 'show' }} Details</button>
      <!-- -->
      <ul v-if="detailsAreVisible">
        <li><strong>Phone:</strong> {{ friend.phone }}</li>
        <li><strong>Email:</strong> {{ friend.email }}</li>
      </ul>
    </li>
  </ul>
</section>
```

```javascript
const app = Vue.createApp({
    data() {
        return {
            detailsAreVisible: false,
            friends: [
                {
                    id: "manuel",
                    name: 'manuel Lorenz',
                    phone: '01234 5678 991',
                    email: 'manuel@localhost.com'
                },
                {
                    id: "julie",
                    name: 'manuel Jones',
                    phone: '1231 23124 23123',
                    email: 'julie@localhost.com'
                },
            ],
        };
    },
    methods: {
        toggleDetails() {
            this.detailsAreVisible = !this.detailsAreVisible;
        }
    }
});

app.mount("#app");
```

```
정리한 html 코드에서 볼 수 있듯이 button을 클릭하면 해당 영역을 show / hide 하고 싶다면 해당 button에 
method를 binding 해주면 된다. 그러나, v-for를 통해 데이터를 나열하게 되면 모든 button에 해당 method가 binding 되지만, 
독립적으로 실행되지 않는다. 가장 처음 단락의 button을 클릭하면 모든 ul 단락이 hide or show 되어 버린다. 
그렇다고 해서 아래와 같이 해당 button의 인자로 friend.id를 넘기고 모든 ul에 그것을 하나씩 나열한다면 매우 번거로운 일이 될 것이다.
```

```html
<section id="app">
  <ul>
    <li v-for="friend in friends" :key="friend.id">
      <h2>{{ friend.name }}</h2>
      <button @click="toggleDetails(friend.id)">{{ detailsAreVisible ? 'hide' : 'show' }} Details</button>
      <ul v-if="detailsAreVisible">
        <li><strong>Phone:</strong> {{ friend.phone }}</li>
        <li><strong>Email:</strong> {{ friend.email }}</li>
      </ul>
    </li>
  </ul>
</section>
```

```javascript
const app = Vue.createApp({
    data() {
        return {
            detailsAreVisibleA: false,
            detailsAreVisibleB: false,
            friends: [
                {
                    id: "manuel",
                    name: 'manuel Lorenz',
                    phone: '01234 5678 991',
                    email: 'manuel@localhost.com'
                },
                {
                    id: "julie",
                    name: 'manuel Jones',
                    phone: '1231 23124 23123',
                    email: 'julie@localhost.com'
                },
            ],
        };
    },
    methods: {
        toggleDetails(id) {
            this.detailsAreVisible = !this.detailsAreVisible;
        }
    }
});

app.mount("#app");
```

```
또 해당 ul에 선언한 data를 어떻게 할 지도 매우 고민이 될 것이다. [될지 안될지..]
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide
