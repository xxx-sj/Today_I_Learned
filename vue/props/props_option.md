# props object
[이전 링크](https://github.com/Jungsangjin0/til/blob/master/vue/props/02.unidirectional_data_flow.md)

```
이전에는 props 의 값으로 간단하게 배열이외에도 나아가 data type 이라던가 default value를 설정할 수 있다.
```

```vue
export default {
  name: "FriendContact",
  // props: ['name', 'phoneNumber', 'emailAddress'],
  props: {
    name: {
      type: String,
      
    },
    phoneNumber: String,
    emailAddress: String,
  },
```

```
props를 value값으로 object를 만들고 property를 추가한다 value는 dataType이다.
```

```
export default {
  name: "FriendContact",
  // props: ['name', 'phoneNumber', 'emailAddress'],
  props: {
    name: {
      type: String,
      required: true,
    },
    phoneNumber: {
      type: String,
      required: true,
    },
    emailAddress: {
      type: String,
      required: true,
    },
    isFavorite: {
      type: String,
      required: false,
      default: function() {},
      validator: function(value) {
        return value === '1' || value === '0';
      }
    }
  },
```

```
혼자 하는경우는 대부분 모든 component의 data를 알고 있기 때문에 필요성을 못 느끼겠지만, 
third-party로써 제공하거나, 팀으로 개발할 경우 해당component를 정의하는데 필요한 값들을 알릴 수 있다.
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide

