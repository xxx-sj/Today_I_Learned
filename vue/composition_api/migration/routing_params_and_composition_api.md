# routing and params with composition api [composition-17]

```
options api에서는 this를 참조하여 this.$route로 url의 pathvariable을 가져올 수 있었다. 
composition api에서는 이러한방법을 몇 가지 제공하는데, 첫 번째로는 기본적으로 param에 props:true 설정으로
props으로 받아오는 방법이다.
```

```js
//main.js

import { createApp } from 'vue';
import { createRouter, createWebHistory } from 'vue-router';

import App from './App.vue';
import AllProducts from './pages/AllProducts.vue';
import ProductDetails from './pages/ProductDetails.vue';
import AddProduct from './pages/AddProduct.vue';

const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', redirect: '/products' },
    { path: '/products', component: AllProducts },
    { path: '/products/:pid', component: ProductDetails, props: true },          //
    { path: '/products/add', component: AddProduct }
  ]
});

const app = createApp(App);

app.use(router);

app.mount('#app');
```

```
route에 props:true를 선언하고 받으려는 component 내부에서 setup method의 인자로 props을 받는다.
```

```vue
//list.vue

<template>
  <section>
    <h2>All products</h2>
    <ul>
      <li v-for="product in products" :key="product.id">
        <h3>{{ product.title }}</h3>
        <h4>${{ product.price }}</h4>
        <p>{{ product.description }}</p>
        <router-link :to="`/products/${product.id}`">View Details</router-link>           //
      </li>
    </ul>
  </section>
</template>

<script>
import { inject } from 'vue';

export default {
  setup() {
    const loadedProducts = inject('products');

    return { products: loadedProducts };
  },
};
</script>

<style scoped>
section {
  margin: 3rem auto;
  max-width: 40rem;
  padding: 1rem;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
}

ul {
  list-style: none;
  margin: 0;
  padding: 0;
}

li {
  margin: 1rem 0;
  border: 1px solid #ccc;
  padding: 1rem;
}

h3, h4 {
  margin: 0.5rem 0;
}
</style>
```

```vue
//detai.vue

<template>
  <section>
    <h2>{{ title }}</h2>
    <h3>${{ price}}</h3>
    <p>{{ description}}</p>
    <router-link to="/products/p2">Product 2</router-link>
  </section>
</template>

<script>
import { inject, computed } from 'vue';

export default {
  props: ['pid'],                                   //
  setup(props) {
    const products = inject('products');             //

    // const title = ref('');
    // const price = ref(null);
    // const description = ref('');
    // console.log({products, props})
    console.log({props})
    const selectedProduct = computed(() => products.value.find(product => product.id === props.pid)   //
    );                                                                                                   //
    const title = computed(()=>selectedProduct.value.title);                                          //
    const price = computed( () => selectedProduct.value.price);                                      //
    const description = computed( () => selectedProduct.value.description);                         //


    return { title, price, description };
  },
};
</script>


<style scoped>
section {
  margin: 3rem auto;
  max-width: 40rem;
  padding: 1rem;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
}
</style>
```

```
detail page에서 router-link 정의했을 때 아래와 같이 정의하면 페이지가 re-rendering 되지 않는다. 
```

```js
<script>
import { inject } from 'vue';

export default {
  props: ['pid'],
  setup(props) {
    const products = inject('products');

    // const title = ref('');
    // const price = ref(null);
    // const description = ref('');
    // console.log({products, props})
    console.log({props})
    const selectedProduct = products.value.find(product => product.id === props.pid);
    const title = selectedProduct.title;
    const price = selectedProduct.price;
    const description = selectedProduct.description;


    return { title, price, description };
  },
};
</script>
```

```
props 값이 변하여 변수에 새로 할당하여도 값은 setup method call 되었을 때 set 했기 때문에 변하지 않기 때문이다 
이 값들을 computed로 변경하면 props 값의 변경을 감지하여 값을 변경한다.
```

```js

<script>
import {computed, inject, watch} from 'vue';

export default {
  props: ['pid'],
  setup(props) {
    const products = inject('products');

    // const title = ref('');
    // const price = ref(null);
    // const description = ref('');
    // console.log({products, props})
    console.log({props})
    const selectedProduct = computed(() => products.value.find(product => product.id === props.pid));
    const title = computed(() => selectedProduct.value.title);
    const price = computed( () => selectedProduct.value.price);
    const description = computed(() =>selectedProduct.value.description);

    watch(selectedProduct, function(newValue) {
      console.log(newValue)
    })


    return { title, price, description };
  },
};
</script>
```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880352#content
