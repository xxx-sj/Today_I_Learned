# iterator_pattern_with_java_iterator


```
컬렉션의 구현 방법[array, list ...]을 노출하지 않으면서 집합체 내의 모든 항목에    
접근하는(iterator)을 제공합니다. 또한 각 항목에 접근할 수 있게 해주는 기능을
집합체[list, array]가 아니라 반복자 객체[iterator]가 책임진다는 장점이 있다.
그러면 집합체 인터페이스와 구현이 간단해지고, 각자에게 중요한 일만 처리할 수 있게 된다.

collection은 iterator를 구현하기 때문에 iterator 메소드를 사용하면 되고, 
array는 iterator를 implement 하여 해당 메소드들을 구현하고 menu라는 interface 에서    
구현이 필요한 createIterator를 통해 Iterator[DinerMenuIterator]를 return 한다.
```


##### java.util.iteraotr 적용하기


```java
class DinerMenuIterator implements Iterator<MenuItem> {
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
    public MenuItem next() {
        MenuItem menuItem = items[position];
        position = position + 1;
        return menuItem;
    }

    @Override
    public void remove() {
        throw new UnsupportedOperationException("메뉴 항목은 지우면 안됩니다.");
    }
}
```

##### 팬케이크와 디너클래스의 메뉴를 통일하기위해 인터페이스 만들기


```java
interface Menu {
    public Iterator<MenuItem> createIterator();
}


class Waitress {
    //구상 -> interface로 변경
//    PancakeHouseMenu pancakeHouseMenu;
    Menu pancakeHouseMenu;
//    DinerMenu dinerMenu;
    Menu dinerMenu;

    //구상클래스에서 menu 인터페이스로 변경
//    public Waitress(PancakeHouseMenu pancakeHouseMenu, DinerMenu dinerMenu) {
    public Waitress(Menu pancakeHouseMenu, Menu dinerMenu) {
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



### iterable
```
향상된 for문 사용하기
```


```java
public void printMenu(Iterator iterator) {
        while(iterator.hasNext()) {
            MenuItem menuItem = iterator.next();
        }
    }
    

public void printMenu(Iterator iterator) {
        for(MenuItem item: menu) {
            //do somethind
        }
    }
    

```

```
iterable구현 시 array는 iterable를 구현하지 않기 때문에 문제가 발생하게된다.
```


### 다른 클래스가 추가되었을 때

```java

class CafeMenu {
    Map<String, MenuItem> menuItems = new HashMap<>();

    public CafeMenu() {
        //addItems ..
    }

    public void addItem() {
        //add item into menuItems
    }

    public Map<String, MenuItem> getMenuItems {
        return menuItems;
    }

}


class CafeMenu implements Menu {
    Map<String, MenuItem> menuItems = new HashMap<>();

    public CafeMenu() {
        //addItems ..
    }

    public void addItem() {
        //add item into menuItems
    }

//    public Map<String, MenuItem> getMenuItems {
//        return menuItems;
//    }

    @Override
    public Iterator<MenuItem> createIterator() {
        return menuItems.values().iterator();
    }
}

```


```java
class Waitress {
    //구상 -> interface로 변경
//    PancakeHouseMenu pancakeHouseMenu;
    Menu pancakeHouseMenu;
//    DinerMenu dinerMenu;
    Menu dinerMenu;
    
    Menu cafeMenu;

    //구상클래스에서 menu 인터페이스로 변경
//    public Waitress(PancakeHouseMenu pancakeHouseMenu, DinerMenu dinerMenu) {
    public Waitress(Menu pancakeHouseMenu, Menu dinerMenu, Menu cafeMenu) {
        this.pancakeHouseMenu = pancakeHouseMenu;
        this.dinerMenu = dinerMenu;
        this.cafeMenu = cafeMenu;
    }

    public void printMenu() {
        Iterator<MenuItem> pancakeHouseMenu = pancakeHouseMenu.createIterator();
        Iterator<MenuItem> dinerIterator = dinerMenu.createIterator();
        Iterator<MenuItem> cafeIterator = cafeMenu.createIterator();
        printMenu();
        printMenu();
    }


}
```

### 종업원코드 코드 개선하기
```
매개변수로 받아오는 객체가 늘어날수록 printMenu를 n번 호출해야한 다는 점과
새로운 메뉴가 추가 될때마다 종업원에 코드를 추가해야 한다는 점이 OCP에 위배된다.
```


```java
 public Waitress(Menu pancakeHouseMenu, Menu dinerMenu, Menu cafeMenu) {
        this.pancakeHouseMenu = pancakeHouseMenu;
        this.dinerMenu = dinerMenu;
        this.cafeMenu = cafeMenu;
    }
    
    public Waitress(List<Menu> menus) {
        this.menus = menus;
    }
    
    
    
    
    public void printMenu() {
        Iterator<MenuItem> pancakeHouseMenu = pancakeHouseMenu.createIterator();
        Iterator<MenuItem> dinerIterator = dinerMenu.createIterator();
        Iterator<MenuItem> cafeIterator = cafeMenu.createIterator();
        printMenu();
        printMenu();
    }
    
    public void printMenu() {
        Iterator<Menu> menuIterator = menus.iterator();
        while(menuIterator.hasNext()) {
            Menu menu = menuIterator.next();
            printMenu(menu.createIterator());
        }
    }
    
    void printMenu(Iterator<MenuItem> iterator) {
        while(iterator.hasNext()) {
            MenuItem menuItem = iterator.next();
        }
    } 
    
```


##### 출처: 헤드퍼스트 디자인 패턴:개정판
