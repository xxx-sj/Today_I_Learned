```
form 태그 사이에 버튼을 생성 후 버튼 클릭 시 submit 되어 페이지가 reload된다 이것을 방지하기 위한 방법으로 form 태그에 [v-on:submit=""] 에 함수를 넣는데,    
기본적인 이벤트를 막는 메소드와 바인딩을 시켜준다.
버튼을 클릭하게 되면 이벤트가 발생하고, 발생한 이벤트는 submit에 바인딩된 메소드의 인자를 통해 전달된다. 
```

```javascript
<form v-on:submit="submitForm">
        <input type="text">
        <button>Sign up</button>
</form>
      
submitForm(event) {
      event.preventDefault();
},
```

```
vue 에서는 위와 같은 수정자를 제공하는데 아래와 같이 사용하면 된다.
```

```javascript
 <form v-on:submit.prevent="submitForm">
        <input type="text">
        <button>Sign up</button>
</form>

submitForm(event) {
      alert("Submitted!");
    },
```


