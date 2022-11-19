# v-show 

```
v-show는 v-if와 같이 else if 나, else를 갖지않고 독립적으로 사용한다.
v-show는 v-if와 같이 동작하지만, 차이점은 v-show를 사용한 태그는 렌더링은 되지만 브라우저에서 보여지지 않는다. [display:none]
반대로 v-if는 렌더링도 되지않는다. [dom에서 요소를 제거하고 추가한다.]
```

```html
<p v-show="goals.length === 0">No goals have been added yet - please start adding some!</p>
```

```
v-show는 요소를 항상 추가하고 제거할 필요가 없습니다. [display:none] 따라서 v-show는 v-if보다 성능저하가 덜하다.
v-show는 visibility 의 변환이 많은 element가 있는경우 사용하는 것이 좋다. 아닌경우라면 v-if를 사용하는 것이 좋다.

v-show는 css로 숨긴것이기 때문에 영역을 차지할 수 있고, v-if는 dom에 렌더링이 되지않기 때문에 영역을 차지하지 않는다.
```



##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide
