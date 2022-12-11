# router link

```
router-link 태크는 a와 비슷하다.
router-link는 이전에 button click으로 component를 변경한것과 같이 동작하며, 
url을 변경하고 component를 변경하는 역할을 한다.
router-link는 렌더링될 때 a 태크로 렌더링된다.

router-link는 to prop을 전달해야 하는데, 이동하려는 path를 전달한다.

router-link는 a tag로 렌더링되지만 브라우저 기본값은 prevented 되어있다. 브라우저는 실제로 
다른 html파일을 로드하려 하지 않는다. 대신 router가 href 속성을 분석하여 router option과 비교하여
component를 load한다.
```

```vue
//TheNavigation.vue

<template>
  <header>
    <nav>
      <ul>
        <li>
          <router-link to='/teams'>Teams</router-link>
        </li>
        <li>
          <router-link to='/users'>Users</router-link>
        </li>
      </ul>
    </nav>
  </header>
</template>

<style scoped>
header {
  width: 100%;
  height: 5rem;
  background-color: #11005c;
}

nav {
  height: 100%;
}

ul {
  list-style: none;
  margin: 0;
  padding: 0;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

li {
  margin: 0 2rem;
}

a {
  background: transparent;
  border: 1px solid transparent;
  cursor: pointer;
  color: white;
  padding: 0.5rem 1.5rem;
  display: inline-block;
}

a:hover,
a:active {
  color: #f1a80a;
  border-color: #f1a80a;
  background-color: #1a037e;
}
</style>
```

**동적 router-link**
```
router-link의 속성 중 to를 동적 바인딩을 통해 전달할 수 있다
 [ <router-link :to="'/teams/' + id" />]
props로 넘어온 id에 따라 to를 동적으로 전달할 수 있다.
```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
