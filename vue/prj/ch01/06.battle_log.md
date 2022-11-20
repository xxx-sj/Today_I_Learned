# 전투 기록 쌓기

```
기록을 쌓기위해 method를 작성한다. 이 method는 3개의 인자를 받는데, 누가, 어떠한 행동을, 얼만큼 했는지를 받아야 한다.
또한 log를 쌓기위해 data 배열이 필요하게 된다.
```

```html
<section id="log" class="container">
<h2>Battle Log</h2>
<ul>
  <li v-for="logMessage in logMessages">
    <!-- class 동적으로 사용시 --는 허용되징 않지만, ''으로 묶으면 가능하다.
     또한 logic이 길어도 따로 computed로 빼지 않는것은 해당 값은 for 내부 에서만 유효하고
     data 값이 아니기 때문에 빼는것은 지양한다.
    -->
    <span
      :class="{'log--player': logMessage.actionBy === 'player',
      'log--monster': logMessage.actionBy === 'monster'}"
    >{{ logMessage.actionBy === 'player' ? 'Player' : 'Monster' }}</span>
    <span v-if="logMessage.actionType === 'heal'">heals himself for
      <span class="log--heal">{{ logMessage.actionValue }}</span> </span>
    <span v-else>
      attacks and deals <span class="log--damage">{{ logMessage.actionValue }}</span>
    </span>
  </li>
</ul>
</section>
```

```javascript
function getRandomValue(min, max) {
    return Math.floor(Math.random() * (max - min)) + min;
}

const app = Vue.createApp({
    data() {
        return {
            playerHealth: 100,
            monsterHealth: 100,
            currentRound: 0,
            winner: null,
            logMessages: [], //
        };
    },

    computed: {
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
        mayUseSpecialAttack() {
            return this.currentRound % 3 !== 0 || this.currentRound === 0;
        },
    },

    watch: {
        playerHealth(value) {
            if (value <= 0 && this.monsterHealth <= 0) {
                //draw
                this.winner = 'draw';
            } else if (value <= 0) {
                //player lost
                this.winner = 'monster';
            }
        },

        monsterHealth(value) {
            if (value <= 0 && this.playerHealth <= 0) {
                //draw
                this.winner = 'draw';
            } else if (value <= 0) {
                //monster lost|
                this.winner = 'player';
            }
        }
    },

    methods: {
        startGame() {
            this.playerHealth = 100;
            this.monsterHealth = 100;
            this.currentRound = 0;
            this.winner = null;
            this.logMessage = []; //
        },
        attackMonster() {
            this.currentRound++;
            const attackValue = getRandomValue(5, 12);
            this.monsterHealth -= attackValue;
            this.addLogMessage('player', 'attack', attackValue); //
            this.attackPlayer();
        },
        attackPlayer() {
            const attackValue = getRandomValue(8, 15);
            this.playerHealth -= attackValue;
            this.addLogMessage('monster', 'attack', attackValue); //
        },
        specialAttackMonster() {
            this.currentRound++;
            const attackValue = getRandomValue(10, 25);
            this.monsterHealth -= attackValue;
            this.addLogMessage('player', 'special-attack', attackValue); //
            this.attackPlayer();
        },

        healPlayer() {
            this.currentRound++;
            const healValue = getRandomValue(8, 20);
            if (this.playerHealth + healValue > 100) {
                this.playerHealth = 100;
            } else {
                this.playerHealth += healValue;
            }
            this.addLogMessage('player', 'heal', healValue); //
            this.attackPlayer();
        },

        surrender() {
            this.winner = 'monster';
        },

        //
        addLogMessage(who, what, value) {
            this.logMessages.unshift({
                actionBy: who,
                actionType: what,
                actionValue: value,
            });
        }
    },
});

app.mount("#game");

```
##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide
