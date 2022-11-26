# template-proper [템플릿]

```
vue에는 template이라는 property가 있다. 단순히 html에서 보자면 mount한 부분이 template이 될 수 있다.
```

```html
<section id="app2">
  <p>{{favoriteMeal}}</p>
</section>
```

```
vue의 option으로 [property]로도 template를 전달 할 수 있다. 이미 control하고 있던 id="app2"의 내부는 template이었다.
이후 mount를 해줘야한다. 이유는 mount해주지않으면 해당 template 을 어디에 mount해야할지 vue는 알 수 없기 때문이다.
```

```html
<section id="app2">

</section>
```

```javascript
const app2 = Vue.createApp({
  template: `
    <p>{{favoriteMeal}}</p>
  `,
  data() {
    return {
      favoriteMeal: "Pizza"
    };
  },
});

app2.mount("#app2")
```

##### 출처:  https://www.udemy.com/course/vuejs-2-the-complete-guide
