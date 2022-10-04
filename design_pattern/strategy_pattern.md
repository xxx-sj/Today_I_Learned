# strategy pattern [전략패턴]

행동을 해당 interface에 위임하고, 해당 interface를 필드로 갖는다.
해당 메소드를 호출할 때 필드로 갖고있는 interface의 method를 호출한다.
(setter or 수정자를 통해 해당 interface[구현체] 를 변경할 수 있다.)

```java
interface AttackInterface {
  public void attack();
}
```

```java
class Tank implements AttackInterface {
   @Override
   public void attack() {
      System.out.println("tack attack.");
   }
}
```

```java
class Airport implements AttackInterface {
   @Override
   public void attack() {
      System.out.println("airport attack.");
   }
}
```

```java
Class Unit {
  private AttackInterface attackInterface;
  
  ...
  //생성자를 통해 interface를 set 한다.
  public Unit(AttackInterface attackInterface) {
    this.attackInterface = attackInterface;
  }
  
  public void attack() {
    attackInterface.attack();
  }
  ...
  
  public void setAttackInterface(AttackInterface attackInterface) {
    this.attackInterface = attackInterface;
  }
}
```java





#### 출처: 헤드퍼스트 디자인패턴 개정판
