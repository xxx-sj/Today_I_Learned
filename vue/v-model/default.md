# v-model

### 사용법
```
input과 같은 입력 field나, 다른 tag에 값을 양방향으로 바인딩하기 위해 사용한다
```

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
