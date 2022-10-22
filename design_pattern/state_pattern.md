# state_pattern [상태 패턴]




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

#####  1/10 확률로 보너스 알맹이를 추가 요청이 들어옴
```
먼저 당첨이 된 상태를 추가해야한다. 해당 상태를 추가하게 되면서    
나머지 메소드에도 조건이 하나씩 추가되버린다.
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




##### 출처: 헤드퍼스트 디자인 패턴: 개정판
