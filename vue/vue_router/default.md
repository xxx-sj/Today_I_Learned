# routing

```
이 전에는 한 페이지에 [ App.vue ] 에 모든 component를 작성하고, js 이벤트에 의해 화면을 전환햇었는데, 
이러한 경우 다른 user에게 페이지를 공유할 때 현재 보고있는 페이지가 아닌 처음 화면 페이지로 이동하게 되어버린다.
의도한것이라면 이것이 맞겠지만 그것이 아니라면 url을 통해 페이지를 구별해 주어야 할 것이다.
```

```
vue-router를 통해 이를 구현할 수 있다.
[ npm install --save vue-router ]
```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
