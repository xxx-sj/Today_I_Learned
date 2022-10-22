# state_pattern [상태 패턴]

```
객체의 내부 상태가 바뀜에 따라서 객체의 행동을 바꿀 수 있다.    
마치 객체의 클래스가 바뀌는 것과 같은 결과를 얻을 수 있다.
```

```
상태를 별도의 클래스로 캡슐화한 다음 현재 상태를 나타내는 객체에 행동을 위임하므로    
내부 상태가 바뀔 때 행동이 달라지게 된다.
```


```java
class GumballMachine {
    final static int SOLD_OUT = 0;
    final static int NO_QUARTER = 1;
    final static int HAS_QUARTER = 2;
    final static int SOLD = 3;

    int state = SOLD_OUT;
    int count = 0;

    public GumballMachine(int count) {
        this.count = count;
        if (count > 0) {
            state = NO_QUARTER;
        }
    }

    //동전을 넣는 경우
    public void insertQuarter() {

        if(state == HAS_QUARTER) {
            System.out.println("plz insert coin just one");
        } else if (state == NO_QUARTER) {
            state = HAS_QUARTER;
            System.out.println("inserted coin");
        } else if (state == SOLD_OUT) {
            System.out.println("sold out next time");
        } else if (state == SOLD) {
            System.out.println("plz wait");
        }
    }
    
    //동전을 반환하는 경우
    public void ejectQuarter() {
        if (state == HAS_QUARTER) {
            System.out.println("동전이 반환된다.");
            state = NO_QUARTER;
        } else if (state == NO_QUARTER) {
            System.out.println("동전을 넣어주세요.");
        } else if (state == SOLD) {
            System.out.println("이미 알맹이를 뽑으셨다.");
        } else if (state == SOLD_OUT) {
            System.out.println("동전을 넣지 않았따. 반환될 동전이 없다.");
        }
    }
    
    //손잡이를 돌리는 경우
    public void turnCrank() {
        if (state == SOLD) {
            System.out.println("손잡이는 한번만..");
        } else if (state == NO_QUARTER) {
            System.out.println("동전을 넣어주세요.");
        } else if (state == SOLD_OUT) {
            System.out.println("매진되었습니다.");
        } else if (state == HAS_QUARTER) {
            System.out.println("손잡이를 돌리셨습니다.");
            state = SOLD;
            dispense();
        }
    }
    
    //알맹이 내보내기
    public void dispense() {
        if (state == SOLD) {
            System.out.println("알맹이를 내보내고 있다.");
            count -= 1;
            if (count == 0) {
                System.out.println("더 이상 알맹이가 없다.");
                state = SOLD_OUT;
            } else {
                state = NO_QUARTER;
            }
          //이 아래는 오류상황
        } else if (state == NO_QUARTER) {
            System.out.println("동전을 넣어주세요.");
        } else if (state == SOLD_OUT) {
            System.out.println("sold out");
        } else if (state == HAS_QUARTER) {
            System.out.println("알맹이를 내보낼 수 없다.");
        }
    }
}

```

##### 바뀌는 부분을 캡슐화한다.

##### 기존 코드를 활용하는 대신 상태 객체들을 별도의 코드에 넣고, 어떤 행동이 일어나면
##### 현재 상태 객체에서 필요한 작업을 처리하게 한다.

1. 뽑기 기계와 관련된 모든 행동에 관한 메소드가 들어있는 state 인터페이스를 정의한다.
2. 다음에 기계의 모든 상태를 대상으로 상태 클래스를 구현해야 한다. 기계가 어떤 상태에 있다면,
   그 상태에 해당하는 상태 클래스가 모든 작업을 책임져야한다.
3. 조건문 코드를 전부 없애고 상태 클래스에 모든 작업을 위임한다.

##### 각 상태의 모든 행동을 한 클래스에 넣는다.

##### state 인터페이스 및 클래스 정의하기 디자인에 들어있는 모든 상태를 캡슐화해서 
##### state 인터페이스를 구현하는 클래스를 만든다.

```java

interface State {
    void insertQuarter();
    void ejectQuarter();
    void turnCrank();
    void dispense();
}
```

```
기존에 GumballMachine에서 정적 정수변수로 구별하던 상태를 State를 구현한 구상클래스로 변경하며,    
state 또한 State 인터페이스로 변경된다.
```

```java

class GumballMachine2 {

        State soldOutState;
        State noQuarterState;
        State hasQuarterState;
        State soldState;

        State state;
        int count = 0;

      public GumballMachine2(int numberGumballs) {
              soldOutState = new SoldOutState(this);
              noQuarterState = new NoQuarterState(this);
              hasQuarterState = new HasQuarterState(this);
              soldState = new SoldState(this);

              this.count = numberGumballs;

              if (numberGumballs > 0) {
                      state = noQuarterState;
              } else {
                      state = soldOutState;
              }
      }


      public void insertQuarter() {
              state.insertQuarter();
      }

      public void ejectQuarter() {
              state.ejectQuarter();
      }

      public void turnCrank() {
              state.turnCrank();
              //dispense는 알맹이를 내보내는 메소드인데 
              //client가 알맹이를 내보내라고 주문하는 건 아니기 때문에 구현 x
              state.dispense();
      }

      public void setState(State state) {
              this.state = state;
      }

      public void releaseBall() {
              System.out.println("알맹이 내보내는 중");
              if (count > 0) {
                      count -= 1;
              }
      }

        public State getSoldOutState() {
                return soldOutState;
        }

        public State getNoQuarterState() {
                return noQuarterState;
        }

        public State getHasQuarterState() {
                return hasQuarterState;
        }

        public State getSoldState() {
                return soldState;
        }

        public State getState() {
                return state;
        }

        public int getCount() {
                return count;
        }
}
```



##### state 구현[구상] 클래스
```java
class NoQuarterState implements State {
        GumballMachine2 gumballMachine;

        public NoQuarterState(GumballMachine2 gumballMachine) {
                this.gumballMachine = gumballMachine;
        }

        @Override
        public void insertQuarter() {
                System.out.println("동전을 넣었습니다.");
                gumballMachine.setState(gumballMachine.getHasQuarterState());
        }

        @Override
        public void ejectQuarter() {
                System.out.println("동전을 넣어 주세요");
        }

        @Override
        public void turnCrank() {
                System.out.println("동전을 넣어주세요.");
        }

        @Override
        public void dispense() {
                System.out.println("동전을 넣어 주세요");
        }
}

class SoldOutState implements State {

        GumballMachine2 gumballMachine;

        public SoldOutState(GumballMachine2 gumballMachine) {
                this.gumballMachine = gumballMachine;
        }

        @Override
        public void insertQuarter() {
                System.out.println("볼이 없습니다.");

        }

        @Override
        public void ejectQuarter() {
                System.out.println("볼이 없습니다.");
        }

        @Override
        public void turnCrank() {
                System.out.println("볼이 없습니다.");
        }

        @Override
        public void dispense() {
                System.out.println("볼이 없습니다.");
        }
}

class HasQuarterState implements State {

        GumballMachine2 gumballMachine;

        public HasQuarterState(GumballMachine2 gumballMachine) {
                this.gumballMachine = gumballMachine;
        }

        @Override
        public void insertQuarter() {
                System.out.println("동전은 하나만..");
        }

        @Override
        public void ejectQuarter() {
                System.out.println("동전이 반환되었습니다.");
                gumballMachine.setState(gumballMachine.getNoQuarterState());
        }

        @Override
        public void turnCrank() {
                System.out.println("돌렸습니다.");
                gumballMachine.setState(gumballMachine.getSoldState());
        }

        @Override
        public void dispense() {
                //이 메소드도 상태에서는 부적절하다.
                System.out.println("알맹이 내보냄");
        }
}


//판매 중인 상태
//사용자가 손잡이를 돌리고 난 후 상태를 말한다.
class SoldState implements State {
        GumballMachine2 gumballMachine;

        public SoldState(GumballMachine2 gumballMachine) {
                this.gumballMachine = gumballMachine;
        }

        //현 상태(sold)에서 부적절한 메소드
        @Override
        public void insertQuarter() {
                System.out.println("알맹이를 내보내고 있다.");
        }
        //현 상태(sold)에서 부적절한 메소드
        @Override
        public void ejectQuarter() {
                System.out.println("이미 알맹이를 뽑으셨습니다.");
        }
        //현 상태(sold)에서 부적절한 메소드
        @Override
        public void turnCrank() {
                System.out.println("손잡이는 한번만 돌려주세요");
        }

      //사용자가 동전을 넣고 손잡이를 돌렸을 때만 현 상태가 될 수 있다.
        @Override
        public void dispense() {
                gumballMachine.releaseBall();
                if (gumballMachine.getCount() > 0) {
                        gumballMachine.setState(gumballMachine.getNoQuarterState());
                } else {
                        System.out.println("공이 없어요");
                        gumballMachine.setState(gumballMachine.getSoldOutState());
                }
        }
}
```

1. 각 상태의 행동을 별개의 클래스로 국지화
2. 관리하기 힘든 if문 제거
3. 각 상태를 변경에는 닫혀 있고, gumballMachine 클래스는 새로운 상태 클래스를 
    추가하는 확장에는 열려 있도록 수정



```
어떠한 행동이 호출되면 그 행동은 현재 상태에 맡겨진다.
ex) ejectQuarter() => state.ejectQuarter(); [state 현 상태 참조변수]
```


```
context = GumballMachine [has state]
(context 의 request 메소드가 호출되면 그 장업은 상태가 처리한다.)    
state = state     
모든 구상 상태 클래스의 공통 인터페이스를 정의한다.
concreteState = sold, soldout, ... state를 구현한다.
```


### 상태패턴과 전략패턴
```
상태 패턴을 사용할 때는 상태 객체에 일련의 행동이 캡슐화된다.
상황에 따라 context 객체에서 여러 상태 객체 중 한 객체에게 모든 행동을 맡기게 된다.
클라이언트는 state를 몰라도 된다.     
하지만 전략패턴을 사용할 때는 일반적으로 클라이언트가 context객체에게 어떠한 전략 객체를    
사용할지를 지정해 준다.     
전략패턴은 주로 실행 시에 전략객체를 변경할 수 있는 유연성을 제공하는 용도로 사용된다.

context 객체에 수많은 조건문을 넣는 대신에 상태 패턴을 사용한다 생각하면 된다.
```


#####  1/10 확률로 보너스 알맹이를 추가 요청이 들어옴
```
먼저 당첨이 된 상태를 추가해야한다. 해당 상태를 추가하게 되면서    
나머지 메소드에도 조건이 하나씩 추가되버린다.
```
1. 보너스승리 상태 필드를 만든다.
```java
class GumballMachine2 {

        State soldOutState;
        State noQuarterState;
        State hasQuarterState;
        State soldState;
        State winnerState;
        ...
        
          public State getWinnerState() {
              return winnerState;
        }
```
2. State를 구현한 ConcreteState를 만든다.

```java
class WinnerState implements State {
        GumballMachine2 gumballMachine;

        public WinnerState(GumballMachine2 gumballMachine) {
                this.gumballMachine = gumballMachine;
        }

        @Override
        public void insertQuarter() {
                System.out.println("없는 상태");

        }

        @Override
        public void ejectQuarter() {
                System.out.println("없는 상태state");
        }

        @Override
        public void turnCrank() {
                System.out.println("없는 state");
        }

        @Override
        public void dispense() {
                gumballMachine.releaseBall();
                //알맹이를 2개 내보내고 soldOutState로 돌아감
                if (gumballMachine.getCount() == 0) {
                        gumballMachine.setState(gumballMachine.getSoldOutState());
                } else {
                        gumballMachine.releaseBall();
                        System.out.println("하나더 !!");
                        if (gumballMachine.getCount() > 0) {
                                gumballMachine.setState(gumballMachine.getNoQuarterState());
                        } else {
                                System.out.println("더 이상 알맹이가 없습니다.");
                                gumballMachine.setState(gumballMachine.getSoldState());
                        }
                }
        }

}

```

3. 10% 당첨 여부를 결정해서 뽑기 기계의 상태를 전환하는 코드 추가 [동전을 넣고 난 후 뽑을 때]

```java
class HasQuarterState implements State {

        GumballMachine2 gumballMachine;
        Random randomWinner = new Random(System.currentTimeMillis());

        public HasQuarterState(GumballMachine2 gumballMachine) {
                this.gumballMachine = gumballMachine;
        }

        @Override
        public void insertQuarter() {
                System.out.println("동전은 하나만..");
        }

        @Override
        public void ejectQuarter() {
                System.out.println("동전이 반환되었습니다.");
                gumballMachine.setState(gumballMachine.getNoQuarterState());
        }

        @Override
        public void turnCrank() {
                System.out.println("돌렸습니다.");
                int winner = randomWinner.nextInt(10);
                if ((winner == 0) && (gumballMachine.getCount() > 1)) {
                        gumballMachine.setState(gumballMachine.getWinnerState());
                } else {
                        gumballMachine.setState(gumballMachine.getSoldState());
                }
                gumballMachine.setState(gumballMachine.getSoldState());
        }

        @Override
        public void dispense() {
                //이 메소드도 상태에서는 부적절하다.
                System.out.println("알맹이 내보냄");
        }
}

```



#### 정상성 검사(sanity check)
1. soldState와 winnerState에는 중복되어 있는 코드가 꽤 많다.
        [state를 추상 클래스로 만들고 몇 가지 기본 기능을 추가하는 것]
        (기계에는 display가 없으니, 메시지를 사용자들에게 보여줄 수 없다.결국 오류 반응은 모두 같으니까 state 추상 클래스에서 상속받기)
2. dispense() 메소드는 항상 호출된다. 동전이 없어도 말이다. 
    turnCrank()에서 boolean을 return 하거나 예외를 도입해서 처리하기
3. 상태 전환 정보는 모두 상태 클래스에 있다. 이로 인해 어떠한 문제가 생길까?
    그 부분을 GumballMachine 클래스로 옮기는건 어떻고 각 장단점을 생각해보기
4. GumballMachine 객체의 인스턴스를 여러 개 만들게 될까요? 그렇다면 상태 인스턴스를   
    정적 인스턴스 변수로 만들어서 공유하는 편이 좋다. 그렇다면 GumballMachine과 상태 클래스를 각각 어떻게 고쳐야 할까?
##### 출처: 헤드퍼스트 디자인 패턴: 개정판
