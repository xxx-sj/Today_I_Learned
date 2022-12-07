# select [select]

```
select 사용 시에도 동일하게 v-model을 사용할 수 있는데, select에서 child 로 사용하는 option 의 value 값을
기본 값으로 설정하면 해당 option이 선택된 상태가 기본이 된다.
v-model에는 option에 value로 넣은 값이 data가 된다.
```

```vue
...

<select id="referrer" name="referrer" v-model='referrer'>
        <option value="google">Google</option>
        <option value="wom">Word of mouth</option>
        <option value="newspaper">Newspaper</option>
</select>
...

export default {
  data() {
    return {
      userName: '',
      userAge: null,
      referrer: 'wom',
    };
  },
  methods: {
    submitForm() {
      console.log('UserName: ' + this.userName);
      this.userName = '';
      console.log("userAge: ");
      console.log(this.userAge);
      console.log(31);
      this.userAge = null;
    }
  }
}
```


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
