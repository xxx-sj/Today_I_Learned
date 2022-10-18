# template_method_pattern [템플릿 메소드 패턴]



```java
class Coffee {

    void prepareRecipe() {
        boilWater();
        brewCoffeeGrinds();
        pourInCup();
        addSugarAndMilk();
    }

    public void boilWater() {
        //do something
    }

    public void brewCoffeeGrinds() {
        //do something
    }

    public void pourInCup() {
        //do something
    }

    public void addSugarAndMilk() {
        //do something
    }
}

class Tee {

    void prepareRecipe() {
        boilWater();
        steepTeBag();
        addLemon();
        pourInCup();
    }

    public void boilWater() {
        //do something
    }

    public void steepTeBag() {
        //do something
    }

    public void addLemon() {
        //do something
    }

    public void pourInCup() {
        //do something
    }
}

```

##### 두 개의 클래스 추상화 하기
```
비슷한 성질을 지닌 메소드를 추상화로 묶는다
brewCoffeeGrinds := steppTeaBag -> brew
addSugerAndMilk := addLemon -> addCondiments
```

```java
abstract class CaffeineBeverage  {
    void prepareRecipe() {
        boilWater();
        brew();
        pourInCup();
        addCondiments();
    };
    abstract void brew();
    abstract void addCondiments();

    void boilWater() {
        //do something
    }

    void pourInCup() {
        //do something
    }
}


class Coffee extends CaffeineBeverage{
    @Override
    void brew() {
        //do something
    }

    @Override
    void addCondiments() {
        //do something
    }
}

class Tee extends CaffeineBeverage {


    @Override
    void brew() {
     //do something
    }

    @Override
    void addCondiments() {
        //do something
    }
}
```

##### 템플릿 메소드는 알고리즘의 각 단계를 정의하며, 서브클래스에서 
##### 일부 단계를 구현할 수 있도록 유도한다
```
샘플코드에서 볼 수 있듯이 두 개의 비슷한 클래스에서 같으 메소드는 상위 클래스로 빼고    
성질이 비슷한 메소드는 추상화를 통해 상위 클래스에서 추상메소드를 만든 후 하위클래스에서
구현하도록 만든다. 해당 메소드들을 call하는 메소드(prepareRecipe)에서 볼 수 있듯이
어떠한 알고리즘의 템플릿(틀) 역할을 하는 메소드가 존재한다.
```

```
알고리즘의 골격을 정의한다. 템플릿 메소드를 사용하면 알고리즘의 일부 단계를 서브클래스에서     
구현할 수 있으며, 알고리즘의 구조는 그대로 유지하면서 알고리즘의 특정 단계를 서브클래스에서 재정의 가능하다.
```


```java

abstract class AbstractClass {

    final void templateMethod() {
        primitiveOperation1();
        primitiveOperation2();
        concreteOperation();
        hook();
    }

    abstract void primitiveOperation1();
    abstract void primitiveOperation2();

    void concreteOperation() {};
    
    void hook() {};
}

```

```
hook method는 추상클래스에서 선언되지만 기본적인 내용만 구현되어 있거나    
아무 코드도 들어있지 않은 메소드입니다. 다양한 위치에서 알고리즘에 끼어들 수도 있고,
그냥 무시하고 넘어가도 되는 메소드이다.
```

##### hook 메소드 사용해보기

```java

abstract class CaffeineBeverageWithHook {
    
    final void prepareRecipe() {
        boilWater();
        brew();
        pourInCup();
        if (customerWantsCondiments()) {
            addCondiments();
        }
    }
    
    abstract void brew();
    abstract void addCondiments();
    
    void boilWater() {
        //do something
    }
    
    void pourInCup() {
        //do something
    }
    
    boolean customerWantsCondiments() {
        return true;
    }
}
```

```java
package main.designPattern.templateMethod;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Locale;

public class HookMethod {
    public static void main(String[] args) {

    }
}


abstract class CaffeineBeverageWithHook {

    final void prepareRecipe() {
        boilWater();
        brew();
        pourInCup();
        if (customerWantsCondiments()) {
            addCondiments();
        }
    }

    abstract void brew();
    abstract void addCondiments();

    void boilWater() {
        //do something
    }

    void pourInCup() {
        //do something
    }

    boolean customerWantsCondiments() {
        return true;
    }
}

class CoffeeWithHook extends CaffeineBeverageWithHook {

    @Override
    void brew() {
        //do something
    }

    @Override
    void addCondiments() {
        //do something
    }

    @Override
    boolean customerWantsCondiments() {
        String answer = getUserInput();

        if (answer.toLowerCase().startsWith("y")) {
            return true;
        } else {
            return; false;
        }
    }

    private String getUserInput() {
        String answer = null;

        System.out.println("~");

        BufferedReader in = new BufferedReader(new InputStreamReader(System.in));

        try {
            answer = in.readLine();
        } catch (IOException e) {
            System.err.println("error");
        }

        if (answer == null) {
            return "no";
        }
        return answer;
    }
}

```


##### 출처: 헤드퍼스트 디자인패턴: 개정판
