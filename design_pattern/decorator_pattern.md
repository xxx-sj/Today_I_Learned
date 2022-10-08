# decorator_pattern [데코레이터 패턴]

##### 객체에 추가 요소를 동적으로 더할 수 있습니다.
##### 데코레이터를 사용하면 서브클래스를 만들 때보다 훨씬 유연하게 기능을 확장할 수 있습니다.

###### 출처[참고]: 헤드퍼스트 디자인 패턴: 개정판

```
데코레이터 패턴에서는 최상단에 component 클래스와 그것을 구현한 [하위 클래스]ConcreteComponent 클래스들과
클래스들을 꾸며주는 Decorator들이 있다. Decorator 하위로 구현클래스들인 ConcreteDecorator클래스들이 있다.
각 데코레이터 안에는 compontnt 객체가 들어가있으며, 구성 요소의 레퍼런스를 포함한 인스턴스변수가 존재하게된다.

```

##### concrete component를 decorator로 감싼다.

```java
//component에 해당한다.
abstract class Beverage {
    public enum Size {TALL, GRANDE, VENEI};
    Size size = Size.TALL;

    String description = "anything";

    public String getDescription() {
        return description;

    }

    public Size getSize() {
        return size;
    }

    public void setSize(Size size) {
        this.size = size;
    }

    public abstract double cost();
}

//decorator 클래스
abstract class CondimentDecorator extends Beverage {
    Beverage beverage; //어떠한 component(concrete)를 감쌀 수 있도록
    
    public abstract String getDescription();
}


//concreteComponent
class Espresso extends Beverage {

    public Espresso() {
        this.description = "에스프레소";
    }

    @Override
    public double cost() {
        //do something
        return 1.99;
    }
}

//concrete Decorator
class Mocha extends CondimentDecorator {

    public Mocha(Beverage beverage) {
        this.beverage = beverage;
    }

    @Override
    public double cost() {
        return beverage.cost() + .20;
    }

    @Override
    public String getDescription() {
        return beverage.getDescription() + ", 모카";
    }
}


class Main {
    public static void main(String[] args) {
        Beverage beverage = new Espresso();

        Beverage beverage1 = new Espresso();
//        beverage1 = new Mocha(beverage1);
//        beverage1 = new Mocha(beverage1);
//        beverage1 = new Mocha(beverage1);
        beverage1 = new Mocha(new Mocha(new Mocha(new Mocha(beverage1))));
    }
}


```
