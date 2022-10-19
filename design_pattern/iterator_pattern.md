# iterator_pattern [반복자 패턴]

```
바뀌는 부분은 캡슐화 하라.
반복자 패턴은 iterator 인터페이스에 의존한다.
```
```java

class MenuItem  {
    String name;
    String description;
    boolean vegetarian;
    double price;

    public MenuItem(String name, String description, boolean vegetarian, double price) {
        this.name = name;
        this.description = description;
        this.vegetarian = vegetarian;
        this.price = price;
    }

    public String getName() {
        return name;
    }

    public String getDescription() {
        return description;
    }

    public double getPrice() {
        return price;
    }

    public boolean isVegetarian() {
        return vegetarian;
    }
}
```


```
하나의 객체에는 배열로 데이터를 저장하고 있고, 다른 하나의 데이터에는 리스트로 데이터를 저장하고 있다.     
이럴경우 getObjects를 하게 되면 각각 가져오는 방법이 달라져서 중복이 생기게 된다.
[List는 get을 통해, array는 index에 접근하여]
이것을 통합하기 위해 iteraotr라는 interface를 확장한 iterator 클래스를 만든다.
기존에 getItems로 가져오는 메소드는 없애고 iterator를 반환하는 메소드를 구현한다.
`getItems로 필드변수를 그대로 반환하면 내부 구조를 다 드러낼 수 있다는 단점이 있다.`
```


```java

interface iterator {
    boolean hasNext();
    MenuItem next();
}

```


```java

class DinerMenuIterator implements Iterator {
    MenuItem[] items;
    int position = 0;

    public DinerMenuIterator(MenuItem[] items) {
        this.items = items;
    }


    @Override
    public boolean hasNext() {
        if (position >= items.length || items[position] == null) {
            return false;
        } else {
         return true;   
        }
    }

    @Override
    public Object next() {
        MenuItem menuItem = items[position];
        position = position + 1;
        return menuItem;
    }
}


```


```java
class DinerMenu {
    static final int MAX_ITEMS = 6;
    int numberOfItems = 0;
    MenuItem[] menuItems;
    
    //...
    
    
    public Iterator createIterator() {
        return new DinerMenuIterator(menuItems);
    }
    //...
}

```


```
iterator 인터페이스를 리턴하는 것을 통해 해당 클래스 내에서 menuItem이 어떻게 관리되는지,   
Iterator 구현클래스 어떤식으로 구현되었는지 알 필요가 없다. 단지 메뉴에 들어있는 하나하나의 객체에
접근할 수 있으면 된다.    
```


```java

class Waitress {
    PancakeHouseMenu pancakeHouseMenu;
    DinerMenu dinerMenu;
    
    public Waitress(PancakeHouseMenu pancakeHouseMenu, DinerMenu dinerMenu) {
        this.pancakeHouseMenu = pancakeHouseMenu;
        this.dinerMenu = dinerMenu;
    }
    
    public void printMenu() {
        Iterator pancakeHouseMenu = pancakeHouseMenu.createIterator();
        iterator dinerIterator = dinerMenu.createIterator();
        
        printMenu();
        printMenu();
    }
    
    public void printMenu(Iterator iterator) {
        while(iterator.hasNext()) {
            MenuItem menuItem = iterator.next();
        }
        
    }
}
```

##### 출처: 헤드퍼스트 디자인패턴: 개정판
