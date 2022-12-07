# v-model

```
input과 같은 입력 field나, 다른 tag에 값을 양방향으로 바인딩하기 위해 사용한다.
v-model은 input의 type에 따라 data가 자동으로 convert 된다.
만약, input의 type이 text라면 [ type=text ] data에 저장되는 값이 string이 되며,
type이 number이라면, data에 저장되는 타입은 number가 된다.
input에 type이 없다면 기본적으로 string이 저장된다.
```

**v-model과는 다르게 ref를 통해 value를 가져올 경우에는 string 타입으로만 가져오게 된다**

```
v-model="[dataName]"
```

```vue

...

<input id="user-name" name="user-name" type="text" v-model='userName' />
 ...


<script>
export default {
  data() {
    return {
      userName: '',
    };
  },
  methods: {
    submitForm() {
      console.log('UserName: ' + this.userName);
      this.userName = '';
    }
  }
}
</script>
```

#####출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
