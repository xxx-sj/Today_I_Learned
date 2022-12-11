# custom css name

```
기본적으로 vue는 css class name으로 v를 prefix로 사용하는데, 이것을 custom 할 수 있다.

transition의 [ name ]attribute로 value를 전달하면 vue는 분석하여 prefix가 name을 전달한 값으로 시작하는
css class를 찾는다. 이렇게 하면 transition을 여러개 사용할 수 있게 된다.
```

```vue

<transition name='para'>
  <p v-if='paraIsVisible'>This is only sometimes visible...</p>
</transition>

.para-enter-from {
  /*opacity: 0;*/
  /*transform: translateY(-30px);*/
}

.para-enter-active {
  /*transition: all 0.3s ease-out;*/
  animation: slide-scale 0.3s ease-out;
}

.para-enter-to {
  /*opacity: 1;*/
  /*transform: translateY(0);*/
}

.para-leave-from {
  /*opacity: 1;*/
  /*transform: translateY(0);*/
}
.para-leave-active {
  /*transition: all 0.3s ease-in;*/
  animation: slide-scale 0.3s ease-in;
}
.para-leave-to {
  /*opacity: 0;*/
  /*transform: translateY(30px);*/
}
```

```
prefix를 변경하는 것 이외에도 *-enter-to class name을 변경할 수도 있다.
transition tag에 enter-to-class , enter-active-class 등을 추가하여 수정할 수 있다
third party css framework를 사용할 때 유용하게 사용할 수 있다.
```

```html
<transition enter-to-class='some-class' enter-active-class='...'>
  <p v-if='paraIsVisible'>This is only sometimes visible...</p>
</transition>
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
