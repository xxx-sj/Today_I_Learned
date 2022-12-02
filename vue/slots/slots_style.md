# slot_style 

```
slot을 사용하여 compontent를 정의할 때 style[scoped 로 정의된]은 vue에서 slot으로 적용되기 전 compile 하고 분석하기
때문에 userInfo에서 썼던 section header와 같은 부분은 적용되지 않는다 [userInfo에서 template 아래 header가 존재한다.]
따라서 slot이 정의된 곳에서 style을 적용해야 한다.
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide
