# Generics 지네릭스


#####장점      
1. 타입 안정성을 제공한다
2. 타입체크와 형변환을 생략할 수 있으므로 코드가 간결해 진다.

```java
package generics;

public class Box<T>{
    T item;
    void setItem(T item) {
        this.item = item;
    }

    T getItem() {
        return item;
    }
}

class Main {
    public static void main(String[] args) {
        Box<String> box = new Box<String>();
        box.setItem("String");
        box.getItem(); //string
        
    }
}
```


##### static멤버는 타입 변수를 사용할 수 없다.
##### 지네릭 타입의 배열을 생성하는 것도 허용되지 않는다. (지네릭 배열 타입의 참조변수 선언 가능)
```
생성 하지 못하는 이유는 new 연산자 때문인데, 이 연산자는 컴파일 시점에 타입 T가 무엇인지 알아야 한다.    
지네릭 배열을 생성해야 할 때는 Reflection API 의 newInstance()를 사용하여 동적으로 생성하거나,    
Object 배열을 생성해서 복사한 다음에 T[]로 형변환 한다.
```


```java
class Box2<T> {
    ArrayList<T> list = new ArrayList<T>();
    
    void add(T item) {
        list.add(item);
    }
    
    T get(int i) {
        return list.get(i);
    }
    
    ArrayList<T> getList() {
        return list;
    }
    
    int size() {
        return list.size();
    }
}
```

##### 객체를 생성할 때는 참조변수[좌측]와 생성자[우측]에 대입된 타입이 일치해야 한다.
```
Box<Apple> appleBox = new Box<Apple>(); //
Box<Apple> appleBox = new Box<Grape>(); // error
```

##### 두 제네릭 타입이 상속관계에 있다 하더라도 마찬가지이다.
```
Box<Fruit> appleBox = new Box<Apple>(); //error
```

##### 단, 두 제네릭 클래스 타입이 상속관계에 있고, 대입된 타입이 같다면 가능하다
```
Box<Apple> appleBox = new FruitBox<Apple>(); // fruitBox는 box의 자손
```

### 제한된 제네릭 클래스 [extends]

##### 특정 타입의 자손들만 대입할 수 있게 제한하기

```java
class FruitBox<T extends Fruit> { //fruit의 자손들만 타입으로 지정가능하다.
    //...
}
```

##### 인터페이스 또한 가능하다.

```java
interface eatable { }

class FruitBox<T extends eatable> {}
```
##### 여러가지의 구현이 포함된 제네릭을 설정할 수 있다.

```java
class FruitBox<T extends eatable & Fruit> {}
```


### 제네릭 와일드 카드 [?]

```
'?'만으로는 object로 타입설정하는 것과 다를것이 없으므로 'extends' 와 'super'로 상한과 하한을 제한 할 수 있다.
<? extends T> 와일드 카드의 상한 제한 T와 그 자손들만 가능하다. 
    <? extends Fruit> -> Fruit, apple, grape ...
<? super T> 와일드 카드의 하한제한, T와 그 조상들만 가능
  <? super Apple> -> Fruit, Apple, object ...
<?> 모두가능
```



##### 출처: 자바의정석
