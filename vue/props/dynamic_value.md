# 동적 속성값 정의
[이전 링크](https://github.com/Jungsangjin0/til/tree/master/vue/props)

```
props로 boolean 값을 넘기려면 어떻게 해야 하는가? 
이 전에는 모든 data가 string 이였다. boolean으로 값을 넘기려고 같은 방식으로 
true or false를 쓴다면 이것은 단지 text에 불과하다.
[ is-favorite="true" / is-favorite="false" is just string(text) ]
string 이외의 값을 사용하기 위해서 v-bind를 사용해야한다.
[ v-bind:is-favorite="true" / :is-favorite="false" ]
v-bind를 사용해서 value를 전달할 때는 js 문법도 사용가능하다.
```

```html
//App.vue

<friend-contact
name='Manuel lorenz'
phone-number='010 2222 2222'
email-address='manuel@localhost.com'
v-bind:is-favorite="true"/>
```
```
custom component도 일반 html element와 같이v-for, v-if를 사용할 수 있다.
```

```vue
//App.vue

<template>
  <section>
    <header><h1>My Friends</h1></header>
    <ul>
      <friend-contact
        v-for="friend in friends"
        :key="friend.id"
        :name='friend.name'
        :phone-number='friend.phone'
        :email-address='friend.email'/>
    </ul>
  </section>
</template>
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide
