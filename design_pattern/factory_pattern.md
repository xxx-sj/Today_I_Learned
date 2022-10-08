# factory_pattern [팩토리 패턴]
### 객체를 생성할 때 필요한 인터페이스를 만듭니다. 어떤 클래스의 인스턴스를 만들지는 서브클래스에서 결정한다.   
### 팩토리 메소드 패턴을 사용하면 클래스 인스턴스 만드는 일을 서브클래스에게 맡기게 된다.
### type(구분)을 통해 하나의 인스턴스를 만들어 낼 때   

```
  - 변수에 구현 클래스의 레퍼런스를 저장하지 않는다.
  - 구현 클래스에서 유도된 클래스를 만들지 않는다.
  - 베이스 클래스에 이미 구현되어 있는 메소드를 오버라이드하지 않는다.
```

```java
public class SimpleUnitFactory {
  
  public Unit createUnit(String name) {
      Unit unit = null;
      
      if (name.equals("marine")) {
        unit = new Marine();
      } else if (name.equals("tank")) {
        unit = new Tank();
        ...
        ..
        
        return unit;
  }
}

public class Group {
  SimpleUnitFactory factory;
  
  public Group(SimpleUnitFactory factory) {
    this.factory = factory;
  }

  public Unit orderUnit(String name) {
      Unit unit;
      
      unit = factory.createUnit(name);
      
      unit.prepare();
      unit.ready();
      unit...
      ...
      return unit;
  }
}
```


##### Pizza(product)와 PizzaStore(Creator)가 있다.   
##### Pizza의 구현체인 CheesePizza(concretePoduct)와 PizzaStore의 구현체인    
##### NYStylePizzaStore(concreteCreator)가 있다.
##### creator를 통해 한개의 메소드는 product를 갖고 행하는 행위에 대한 로직을 담당하고
##### 다른 메소드는 추상화를 통해 이 후 구현클래스에서 해당 product에 대한 구현체를 리턴한다
##### 이때 구현체를 받아오는 메소드는 추상화를 강요하여 받을 수 있게 한다.


```java
class Pizza {
    String name;
    String dough;
    String sauce;
    List<String> toppings = new ArrayList<>();

    public void prepare() {};
    public void bake() {};
    public void cut() {};
    public void box() {};

    public String getName() {
        return this.name;
    }
}

class CheesePizza extends Pizza {

    public void prepare() {//치즈피자만의 준비법};
    public void bake() {//굽는법};
    public void cut() {//자르는법};
    public void box() {//포장하는법..};

    public String getName() {
        return this.name;
    }
}


abstract class PizzaStore {

    public Pizza orderPizza(String type) {

        Pizza pizza;

        pizza = createPizza(type);

        pizza.prepare();
        pizza.bake();
        pizza.cut();
        pizza.box();

        return pizza;
    }

    abstract Pizza createPizza(String type);
}


class NYStylePizzaStore extends PizzaStore {

    @Override
    Pizza createPizza(String type) {
        Pizza pizza = null;
        if (type.equals("Cheese")) {
        //구체적인 구현체 리턴
            pizza = new CheesePizza();
        } else if (type.equals("Pepperoni")) {
        //구체적인 구현체 리턴
            pizza = null;
        }

        return pizza;
    }
}



class Scratch {
    public static void main(String[] args) {
        PizzaStore nypizzaStore = new NYStylePizzaStore();
        PizzaStore krpizzaStore = new KoreaStylePizzaStore();
        Pizza pizza = nypizzaStore.orderPizza("cheese");
        pizza = krpizzaStore.orderPizza("cheese");

    }
}



```


### 추상팩토리 패턴

```
 구상클래스에 의존하지 않고도 서로 연관되거나 의존적인 객체로 이루어진   
 제품군을 생산하는 인터페이스를 제공한다. 구상 클래스는 서로 서브클래스에서 만든다.
```

##### 원재료가 추가되면서 변경되는 코드
##### 피자가 국가마다 준비하는 스타일이 다 다르기 때문에 원재료에 대한
##### 팩토리를 만들고 해당 팩토리클래스를 setter 또는 생성자로 받는다.


```
피자의 준비 시 필요한 원재료에 대한 메소드를 추상메소드를 변경 한다.
피자(product)를 구현하는 클래스(concretePoroduct)에서는 필드로 해당 원재료 인터페이스를    
갖는다. 이후 setter 또는 생성자를 통해 주입받는다. prepare메소드에서는 해당 인터페이스를    
갖고 원재료를 만들어낸다. 피자를 만들어내는 클래스(creator) pizzaStore구현체에서는    
원재료 구현체객체를 생성하여 type에 따라 pizza 구현체(concretePoduct)에 생성자로 넘겨준다.
```

##### product가 필요한 객체들이 다양할 경우 해당 객체에 대한 팩토리를 만들어 사용한다.
##### 해당 팩토리는 concreteProduct에서 필드로 interface를 갖고 있다가, 주입을 받으면
##### 필요로하는 메소드에서 사용한다.


```java
class Pizza {
    String name;
    String dough;
    String sauce;
    List<String> toppings = new ArrayList<>();

    public abstract void prepare() {}; // 준비 시 원재료에 대한 구현체를 받아온다.
    public void bake() {};
    public void cut() {};
    public void box() {};

    public String getName() {
        return this.name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
    
}

class CheesePizza extends Pizza {

    PizzaIngredientFactory ingredientFactory;

    public CheesePizza(PizzaIngredientFactory ingredientFactory) {
        this.ingredientFactory = ingredientFactory;
    }

    @Override
    public void prepare() {
        //현재는 pizza 메소드 prepare은 추상화되어있는 상태
        // dough = ingredientFactory.createDough();
        //...
    }
}


interface  PizzaIngredientFactory{
    //원재료 만드는 메소드 추상화...
}

//원재료 인터페이스를 구현하여 해당 메소드마다 구현메소드를 리턴한다

class NYPizzaIngredientFactory implements PizzaIngredientFactory {

    //...
    //public Dough createDough() {
    //  return new ThinDough();
    //}
}

class JpPizzaIngredientFactory implements PizzaIngredientFactory {

    //...
    //public Dough createDough() {
    //  return new ThinDough();
    //}
}


class JpPizzaStore extends PizzaStore {

    @Override
    Pizza createPizza(String type) {
        Pizza pizza = null;
        PizzaIngredientFactory ingredientFactory = new JpPizzaIngredientFactory();

        if(type.equals("cheese")) {

            pizza = new CheesePizza(ingredientFactory);
            pizza.setName("일본스타일 치즈피자");
        } else if (type.equals("clam")) {
            //...
        } //...
        return pizza;
    }
}
```
![image](https://user-images.githubusercontent.com/62305110/194689761-1159d39d-e809-48f2-b870-07e1eb272cf4.png)




##### 출처: 헤드퍼스트 디자인 패턴:개정판
