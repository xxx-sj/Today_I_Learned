# 다듬기
```
health bar가 [width: ''%] -될 경우 width: 0% 가 되도록 만들어야한다.
```

```javascript
monsterBarStyles() {
    if (this.monsterHealth < 0) {
         return {width: '0%'};
    }
    return {width: this.monsterHealth + '%'};
},
playerBarStyles() {
    if (this.playerHealth < 0) {
        return {width: '0%'};
    }
    return {width: this.playerHealth + '%'};
},
```

```
여기서 computed 속성에서 if문 내부에서 this.monsterHealth = 0 으로 초기화 하지않은 이유를 
짐작해보자면, computed는 계산된 속성을 return 하는것으로써 해당 값의 변경을 지양하는 듯 하다.
```

```
게임이 끝난 후에도 [playerHealth === 0 or monsterHealth === 0] 게임을 다시 시작할 수 있도록 button을 추가한다.
```

```html
<section class="container" v-if="winner">
  <h2>Game Over!</h2>
  <h3 v-if="winner === 'monster'">You Lost!</h3>
  <h3 v-else-if="winner === 'player'">You Won!</h3>
  <h3 v-else>It's a Draw!</h3>
  <button @click="startGame">Start New Game</button>
</section>
```

```javascript
 methods: {
        startGame() {
            this.playerHealth = 100;
            this.monsterHealth = 100;
            this.currentRound = 0;
            this.winner = null;
        },
}
...
```

```
surrender method 추가하기. 항복 이후에는 button을 클릭하지 못하도록 변경하기. 
여기서는 winner가 결정되면 게임이 끝나는 것이므로 위에서 winner가 나오게되면 text가 나오도록 조건문을 넣었었다 [v-if="winner"]
이것을 이용하여 해당 button section 에 winner가 존재한다면 해당 section이 보이지 않게 만든다.
```

```html
<section class="container" v-if="winner">
  <h2>Game Over!</h2>
  <h3 v-if="winner === 'monster'">You Lost!</h3>
  <h3 v-else-if="winner === 'player'">You Won!</h3>
  <h3 v-else>It's a Draw!</h3>
  <button @click="startGame">Start New Game</button>
</section>
<section id="controls" v-else>
  <button @click="attackMonster">ATTACK</button>
  <button :disabled="mayUseSpecialAttack" @click="specialAttackMonster">SPECIAL ATTACK</button>
  <button @click="healPlayer">HEAL</button>
  <button @click="surrender">SURRENDER</button>
</section>
```
```javascript
surrender() {
    this.winner = 'monster';
}
```

##### 출처 : https://www.udemy.com/course/vuejs-2-the-complete-guide
