# custom event with provide and inject

[이전 링크](https://github.com/Jungsangjin0/til/tree/master/vue/provide_inject)

```
일급함수로써 method를 inject를 통해 넘겨 event가 발생되는 곳에서 실행되도록 한다.
```

```vue
//App.vue

<template>
  <div>
    <active-element
      :topic-title="activeTopic && activeTopic.title"
      :text="activeTopic && activeTopic.fullText"
    ></active-element>
    <knowledge-base></knowledge-base> //
  </div>
</template>

<script>
export default {
  data() {
    return {
      topics: [
       ...
       ],
      activeTopic: null,
    };
  },

  provide() {
    return {
      topics: this.topics,
      selectTopic: this.activateTopic, //2
    };
  },

  methods: {
    activateTopic(topicId) {
      this.activeTopic = this.topics.find((topic) => topic.id === topicId);
    },
  },
  mounted() {
    setTimeout(() => {
     this.topics.push({
       id: 'events',
       title: 'Events',
       description: 'Events are important in Vue',
       fullText: 'Events allow you to trigger code on demand'

     })
    }, 3000);
  }
};
</script>
```

```
(2)이처럼 method를 execute 하지않고, method만을 넘겨주게된다. provide를 통해 넘겨진 method를 
사용할 곳에서 inject를 통해 받으면 된다. [ knowledge-element ] parent comonent에서 id를 필요로 하기 때문에
knowledge-element 이벤트가 발생하는 element에서 id를 인자로 넘겨준다.
```

```vue
//Knowledge-base.vue

<template>
  <section>
    <h2>Select a Topic</h2>
    <knowledge-grid></knowledge-grid>
  </section>
</template>

<script>
export default {};
</script>
```

```vue
//knowledge-grid

<template>
  <ul>
    <knowledge-element
      v-for="topic in topics"
      :key="topic.id"
      :id="topic.id"
      :topic-name="topic.title"
      :description="topic.description"
    ></knowledge-element>
  </ul>
</template>

<script>
export default {
  inject: ['topics'],
};
</script>
```

```vue
//knowledge-element

<template>
  <li>
    <h3>{{ topicName }}</h3>
    <p>{{ description }}</p>
    <button @click="selectTopic(id)">Learn More</button>
  </li>
</template>

<script>
export default {
  inject: ['selectTopic'],
  props: ['id', 'topicName', 'description'],
  emits: ['select-topic'],
};
</script>
```

##### 출처 : https://www.udemy.com/course/vuejs-2-the-complete-guide
