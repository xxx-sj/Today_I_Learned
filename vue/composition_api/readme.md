# composition api



### setup method 는 created 이전에 call되기 때문에 this가 없다.


OPTIONS API                             Composition api

data() {}                               ref(), reactive()
methods: { methodName() {}}             function methodName() {}
computed: {val () {}}                   const val = computed(function() { ... })
watch: {}                               watch([ref... ], (newVal, oldValue)=> {});
provide: {} / inject: []                provide(key, val[ref, reactive..]) / const ... = inject(key)


### options api에서 사용하던 lifecylce method는 composition api에서 더 이상 추가하지 않는다.
### 대신 vue에서 이러한 function을 가져와 setup에서 사용할 수 있다. 대신, beforeCreate, created와 동등한 것은 없다.
### setup method가 이러한 hook을 대신하기 때문이다. [비슷한 타이밍에 call 됨] setup method에서 처리하기


beforeCreate, created                   Not Needed (setup() replaces these hooks)
beforeMount, mounted                     onBeforeMount, onMounted
beforeUpdate, updated                   onBeforeUpdate, onUpdated
beforeUnmount, unmounted                onBeforeUnmount, onUnMounted
