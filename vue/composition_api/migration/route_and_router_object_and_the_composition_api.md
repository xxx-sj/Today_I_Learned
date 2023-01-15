# route and router object and the composition api

```
vue-router에서 제공하는 hooks or compotable func or custom composition function provied by the vue-router 을 사용한다.
```

```js
<script>
import {computed, inject, watch} from 'vue';
import { useRoute } from 'vue-router';                        //

export default {
  props: ['pid'],
  setup(props) {
    const products = inject('products');

    const route = useRoute();                                //
    console.log({route})                                      //

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

![image](https://user-images.githubusercontent.com/62305110/212531244-c143fecf-8f06-47fe-9d5e-5e92f13c8e2b.png)

```
route 또한 reactive한 object이기 때문에 watch, computed 에 사용할 수 있다.
```

```js
const selectedProduct = computed(() => products.value.find(product => product.id === route.params.pid));
const title = computed(() => selectedProduct.value.title);
const price = computed( () => selectedProduct.value.price);
const description = computed(() =>selectedProduct.value.description);
```

router를 통해 페이지 전환을 할 때 options api에서는 
```js
this.$router.push(path);
```
로 전달하여 페이지를 전환하였지만 setup method 내에서는 this에 접근을 할 수 없기 때문에
vue-router에서 제공하는 useRouter를 사용하여 동일하게 사용할 수 있다.

```js
<script>
import { ref, inject } from 'vue';
import { useRouter } from 'vue-router'               //

export default {
  setup() {
    const addProduct = inject('addProduct');
    const router = useRouter();                        //

    const enteredTitle = ref('');
    const enteredPrice = ref(null);
    const enteredDescription = ref('');

    function submitForm() {
      addProduct({
        title: enteredTitle,
        description: enteredDescription,
        price: enteredPrice,
      });

      // this.$router.push('/products');               //
      router.push('/products');                        //
    }

    return {
      enteredTitle,
      enteredPrice,
      enteredDescription,
      submitForm,
    };
  },
};
</script>
```



##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880354#content
