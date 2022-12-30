# solution 02

```
App.vue에 남아있는 provide 코드를 없앤다. store의 getter를 통해 값을 제공한다.
```

```vue
//App.vue

<template>
  <the-header></the-header>
  <router-view></router-view>
</template>

<script>
import TheHeader from './components/nav/TheHeader.vue';

export default {
  components: {
    TheHeader
  },
  data() {
    return {
      isLoggedIn: false,
    };
  },
  // provide() {
  //   return {
  //     isLoggedIn: this.isLoggedIn,
  //     cart: this.cart,
  //     addProductToCart: this.addProductToCart,
  //     removeProductFromCart: this.removeProductFromCart,
  //     login: this.login,
  //     logout: this.logout,
  //   };
  // },
  methods: {
    login() {
      this.isLoggedIn = true;
    },
    logout() {
      this.isLoggedIn = false;
    },
  },
};
</script>
```

```js
//ProductList.vue

<template>
  <section>
    <ul>
      <product-item
        v-for="prod in products"
        :key="prod.id"
        :id="prod.id"
        :title="prod.title"
        :image="prod.image"
        :description="prod.description"
        :price="prod.price"
      ></product-item>
    </ul>
  </section>
</template>

<script>
import ProductItem from '../components/products/ProductItem.vue';

export default {
  components: {
    ProductItem,
  },
  computed: {
    products() {
      return this.$store.getters['prods/products'] //
    }
  }
};
</script>

<style scoped>
  ul {
    list-style: none;
    margin: 2rem auto;
    padding: 0;
    max-width: 40rem;
  }
</style>
```

```vue
//nav/TheHeader.vue

<template>
  <header>
    <h1>
      <router-link to="/">VueShop</router-link>
    </h1>
    <nav>
      <ul>
        <li>
          <router-link to="/products">Products</router-link>
        </li>
        <li>
          <router-link to="/cart">Cart</router-link>
          <base-badge mode="elegant">{{ cartQuantity }}</base-badge>
        </li>
        <li v-if="isLoggedIn">
          <router-link to="/admin">Admin</router-link>
        </li>
      </ul>
    </nav>
    <div>
      <button v-if="!isLoggedIn" @click="login">Login</button>
      <button v-if="isLoggedIn" @click="logout">Logout</button>
    </div>
  </header>
</template>

<script>
export default {
  computed: {
    cartQuantity() {
      return this.$store.getters['cart/quantity'];
    }
  }
};
</script>

<style scoped>
header {
  height: 5rem;
  background-color: white;
  margin: 0 10%;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

ul {
  list-style: none;
  margin: 0;
  padding: 0;
  display: flex;
  justify-self: center;
  align-items: center;
}

li {
  margin: 0 1rem;
}

a {
  text-decoration: none;
  color: #333;
  font-weight: bold;
  border-bottom: 2px solid transparent;
  padding-bottom: 0.25rem;
}

a:hover,
a:active,
a.router-link-active {
  color: #45006d;
  border-color: #45006d;
}

button {
  font: inherit;
  cursor: pointer;
  padding: 0.5rem 1.5rem;
  border: 1px solid #45006d;
  background-color: transparent;
  color: #45006d;
  border-radius: 30px;
}

button:hover,
button:active {
  background-color: #f0d5ff;
}
</style>
```

```vue
// pages/UserCart.vue

<template>
  <section>
    <h2>Your Cart</h2>
    <h3>Total Amount: <base-badge mode="elegant">${{ cartTotal }}</base-badge></h3>
    <ul>
      <cart-item
        v-for="item in cartItem"
        :key="item.productId"
        :prod-id="item.productId"
        :title="item.title"
        :image="item.image"
        :price="item.price"
        :qty="item.qty"
      ></cart-item>
    </ul>
  </section>
</template>

<script>
import CartItem from '../components/cart/CartItem.vue';

export default {
  components: {
    CartItem,
  },
  computed: {
    cartTotal() {
      return this.$store.getters['cart/totalSum'] //
    },
    cartItem() {
      return this.$store.getters['cart/products'] //
    }
  }
};
</script>

<style scoped>
section {
  margin: 2rem auto;
  max-width: 40rem;
}

h2 {
  color: #292929;
  text-align: center;
  border-bottom: 2px solid #ccc;
  padding-bottom: 1rem;
}

h3 {
 text-align: center;
}

ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>
```

```
이전에 inject로 받은 method 대신 actions를 사용한다.
```

```vue
//products/productItem.vue

<template>
  <li class="product">
    <div class="product__data">
      <div class="product__image">
        <img :src="image" :alt="title" />
      </div>
      <div class="product__text">
        <h3>{{ title }}</h3>
        <base-badge mode="highlight" :no-margin-left="true">
          <h4>${{ price }}</h4>
        </base-badge>
        <p>{{ description }}</p>
      </div>
    </div>
    <div class="product__actions">
      <button @click="addToCart">Add to Cart</button>
    </div>
  </li>
</template>

<script>
export default {
  props: ['id', 'image', 'title', 'price', 'description'],
  methods: {
    addToCart() {
      this.$store.dispatch('cart/addToCart', { //
        id: this.id,
        image: this.image,
        title: this.title,
        price: this.price,
      });
    },
  },
};
</script>

<style scoped>
li {
  margin: 1.5rem auto;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  padding: 1rem;
}

.product__data {
  display: flex;
}

.product__image {
  margin-right: 1rem;
}

.product__image img {
  height: 10rem;
  width: 10rem;
  object-fit: cover;
}

.product__text h3 {
  margin: 0 0 0.5rem 0;
}

.product__text h4 {
  margin: 0;
}

.product__actions {
  text-align: center;
}

button {
  font: inherit;
  cursor: pointer;
  background-color: #45006d;
  color: white;
  border: 1px solid #45006d;
  padding: 0.5rem 1.5rem;
  border-radius: 30px;
}

button:hover,
button:active {
  background-color: #760ab4;
  border-color: #760ab4;
}
</style>
```

```vue
//cart/CartItme.vue

<template>
  <li>
    <div>
      <img :src="image" :alt="title" />
    </div>
    <div>
      <h3>{{ title }}</h3>
      <div class="item__data">
        <div>
          Price per Item:
          <strong>${{ price }}</strong>
        </div>
        <div>
          Quantity:
          <strong>{{ qty }}</strong>
        </div>
      </div>
      <div class="item__total">Total: ${{ itemTotal }}</div>
      <button @click="remove">Remove</button>
    </div>
  </li>
</template>

<script>
export default {
  props: ['prodId', 'title', 'image', 'price', 'qty'],
  computed: {
    itemTotal() {
      return (this.price * this.qty).toFixed(2);
    }
  },
  methods: {
    remove() {
      this.$store.dispatch('cart/removeFromCart', {productId: this.prodId})
      // this.removeProductFromCart(this.prodId);
    }
  }
};
</script>

<style scoped>
li {
  margin: 1rem auto;
  padding: 1rem;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  text-align: center;
  max-width: 25rem;
}

img {
  width: 5rem;
  height: 5rem;
  border-radius: 50%;
  object-fit: cover;
}

.item__data {
  display: flex;
  justify-content: space-between;
}

.item__total {
  font-weight: bold;
  margin: 1rem 0;
  border-top: 1px solid #474747;
  border-bottom: 2px solid #474747;
  padding: 0.25rem 0;
  width: auto;
}

button {
  font: inherit;
  border: 1px solid #8f0030;
  background-color: #8f0030;
  color: white;
  border-radius: 30px;
  cursor: pointer;
  padding: 0.5rem 1.5rem;
}

button:hover,
button:active {
  background-color: #53001c;
  border-color: #53001c;
}
</style>
```

```
현재 productItem에서 dispatch 로 product값을 전부 넘기고 있지만, 현재 product는 store에 저장되어있고,
식별자인 id만 넘겨 찾을 수 있다. 
```

```vue
//ProductItem.vue

<template>
  <li class="product">
    <div class="product__data">
      <div class="product__image">
        <img :src="image" :alt="title" />
      </div>
      <div class="product__text">
        <h3>{{ title }}</h3>
        <base-badge mode="highlight" :no-margin-left="true">
          <h4>${{ price }}</h4>
        </base-badge>
        <p>{{ description }}</p>
      </div>
    </div>
    <div class="product__actions">
      <button @click="addToCart">Add to Cart</button>
    </div>
  </li>
</template>

<script>
export default {
  props: ['id', 'image', 'title', 'price', 'description'],
  methods: {
    addToCart() {
      this.$store.dispatch('cart/addToCart', { //
        id: this.id,
        // image: this.image,
        // title: this.title,
        // price: this.price,
      });
    },
  },
};
</script>
```

```
cart.js 에서는 이제 payload로 product object가 아닌 id를 받게되며, id를 갖고 product를 조회해야 한다.
state 값은 local로 되어있기 때문에 다른 store에서 직접적으로 접근이 불가능 하지만 action에서 context를 이용하여 접근
할 수 있다. rootState 나, rootGetters 이다. rootGetters로 다른 store의 getters [name space] 도 접근할 수 있다.
```

```js
//cart.js

export default {
  namespaced: true,

  state() {
    return {
      items: [],
      total: 0,
      qty: 0,
    }
  },

  mutations: {
    addProductToCart(state, payload) {
      const productData = payload;
      const productInCartIndex = state.items.findIndex(
        (ci) => ci.productId === productData.id
      );

      if (productInCartIndex >= 0) {
        state.items[productInCartIndex].qty++;
      } else {
        const newItem = {
          productId: productData.id,
          title: productData.title,
          image: productData.image,
          price: productData.price,
          qty: 1,
        };
        state.items.push(newItem);
      }
      state.qty++;
      state.total += productData.price;
    },

    removeProductFromCart(state, payload) {
      const prodId = payload.productId;
      const productInCartIndex = state.items.findIndex(
        (cartItem) => cartItem.productId === prodId
      );
      const prodData = state.items[productInCartIndex];
      state.items.splice(productInCartIndex, 1);
      state.qty -= prodData.qty;
      state.total -= prodData.price * prodData.qty;
    },
  },

  actions: {
    addToCart(context, payload) {
      const prodId = payload.id; // id를 component에서부터 받아서 처리하는 곳
      let products = context.rootGetters['prods/products']; // rootGetters를 통해 다른 store에 접근하는 것
      const product = products.find(prod => prod.id == prodId); // find

      // context.commit('addProductToCart', payload);
      context.commit('addProductToCart', product); // 찾은 product를 인자로 넘긴다.
    },
    removeFromCart(context, payload) {
      context.commit('removeProductFromCart', payload);
    }
  },

  getters: {
    products(state) {
      return state.items;
    },
    totalSum(state) {
      return state.total.toFixed(2);
    },
    quantity(state) {
      return state.qty;
    },

  }
}

```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
