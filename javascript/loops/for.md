# for loop

```
for (initial, condition, increment.decremnent) {
}
```

```js
for (let i = 0; i <-5 ; i++) {
  console.log(i);
}
```

**for of**
```
주로 배열에 사용되며 인덱스가 필요 없는경우 사용한다.
for(const element of arr) {}
```

```js
const numbers = [1, 2, 3, 4, 5]

for(const num of numbers) {
  console.log(num); // 1 2 3 4 5
}
```


**break**

```
loop를 중간에 중단할 때 사용한다.
```

```js
for(let i = 0; i <= 5; i++) {
  if(i == 3) {
    break;
  }
  console.log(i);
}

//0 1 2
```

**continue**
```
loop 중간에 특정 반복을 건너뛰고 다음 루프를 진행한다.
```

```js
for(let i = 0; i <= 5; i++) {
  if(i == 3) continue;
  console.log(i);
}

// 0 1 2 4 5
```
