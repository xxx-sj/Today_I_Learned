# query string

```
쿼리 스트링은 옵션으로 필수는 아니다.
```

```
파라미터에 쿼리스트링을 추가할 수 있는데 단순히 string으로 추가할 수 있으며, [ // return `/teams/${this.id}?sort=asc`;] 
object로도 넘길 수 있다. [ return { name: 'team-members', params: {'teamId': this.id}, query: { sort: 'asc'} }; ]
```

```
전달한 쿼리 스트링은 this.$route object 내에 담겨 있으며 
this.$route.query를 통해 object 를 거낼 수 있다.
query string은 dynamic segment와는 달리 prop으로 전달하지 않는다.
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
