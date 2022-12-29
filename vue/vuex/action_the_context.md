# the action "context"

```
async로 처리할 일이 있을 경우 action을 사용하는 것이 좋다. 이 전에는 인자로 전달받은 context에서 commit만을 [mutation]
을 사용했는데, 더 많은 것을 사용할 수 있다.
```
![image](https://user-images.githubusercontent.com/62305110/209907773-3d269269-c56b-4265-bf62-4723d39ecd0d.png)

```
image에서 볼수 있듯이 dispatch가 존재하여 action에서 다른 action을 call할 수 있다. getter처럼 또한
getters를 사용할 수 있는데, 계산된 값을 사용할 때 사용한다. 또한, state를 접근할 수 있는데 getters로 충분하지 않을 때 접근하는 것!
그래도 action 내부에서 state를 직접적으로 조작해서는 안된다. 변경을 위해서는 항상 mutation을 사용해야 한다.

```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
