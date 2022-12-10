# http request

```
built in method인 fetch를 사용한다.
or third party인 axios도 좋다.
```

```js
fetch(input: RequestInfo | URL, init?: RequestInit): Promise<Response>;
```

```
firebase로는 데이터를 받거나/넘길 때 url 이후 json파일을 넘겨아한다.
fetch("url/test.json", {})
```

```
fetch의 2번 째 인자로 request option을 객체로 넘긴다.
method는 http method를 나타내고, [get, post, delete..]
headers는 request에 전달할 message header에 관한 데이터이고,
body는 request body에 해당한다.
```

```vue
fetch('https://vue-http-demo-2273e-default-rtdb.firebaseio.com/surveys.json', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({ name: this.enteredName, rating: this.chosenRating }),
})
```

```
body에 js object를 담아서 보내게되는데, js object를 json 형태로 보내기 위해
js function인 JSON.stringify를 통해 json 형태로 변경한다.
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
