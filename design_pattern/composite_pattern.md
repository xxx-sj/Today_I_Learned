# composite_pattern [컴포지트 패턴]

##### 반복자 패턴에 이어서
```
객체를 트리구조로 구성해서 부분-전체 계층구조를 구현한다.
컴포지트 패턴을 사용하면 클라이언트에서 개별 객체와 복합 객체를    
똑같은 방법으로 다룰 수 있다.
부분-전체 계층구조
부분들이 계층을 이루고 있지만 모든 부분을 묶어서 전체로 다룰 수 있는 구조
```


```
waitree : menuComponent 인터페이스를 사용해서 menu와 menuitem에 모두 접근한다.
menuComponent : menuitem과 menu 모두 적용되는 인터페이스 이다. 인터페이스가 아닌 추상클래스로 정의
menuitem: print를 오버라이드한다. menuitem에서 쓰일법한 메소드만 오버라이드 한다.
menu: 쓰일법한 메소드만 오버라이드한다.
```

##### menuComponent: leaf(menuitem) 와 node(menu)에서 모두 사용되는 인터페이스

```
잎과 노드는 하는 역할이 다르므로 모든 메소드에 알맞는 기본 메소드 구현은 불가능하다.     
그래서 자기 역할에 맞지 않는 상황을 기준으로 예외를 던지는 코드를 기본 구현으로 제곤한다.
```

```java
abstract class MenuComponent {
    public void add(MenuComponent menuComponent) {
        throw new UnsupportedOperationException();
    }
    
    public void remove(MenuComponent menuComponent) {
        throw new UnsupportedOperationException();
    }
    
    public MenuComponent getChild(int i) {
        throw new UnsupportedOperationException();
    }
     
      
    public String getName() {
        throw new UnsupportedOperationException();
    }
    
    public String getDescription() {
        throw new UnsupportedOperationException();
    }
    
    public double getPrice() {
        throw new UnsupportedOperationException();
    }
    
    public boolean isVegetarian() {
        throw new UnsupportedOperationException();
    }
    
    public void print() {
        throw new UnsupportedOperationException();
    }
}
```

##### leaf [menuItem]
```java
class MenuItem extends MenuComponent {
    String name;
    String description;
    boolean vegetarian;
    double price;

    public MenuItem(String name, String description, boolean vegetarian,
                    double price) {
        this.name = name;
        this.description = description;
        this.vegetarian = vegetarian;
        this.price = price;
    }

    @Override
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    @Override
    public boolean isVegetarian() {
        return vegetarian;
    }

    public void setVegetarian(boolean vegetarian) {
        this.vegetarian = vegetarian;
    }

    @Override
    public double getPrice() {
        return price;
    }

    public void setPrice(double price) {
        this.price = price;
    }

    @Override
    public void print() {
        System.out.println();
        if (isVegetarian()) {
            //do something
        }
        System.out.println();
    }
}

```


##### node [menu]

```java
class Menu extends MenuComponent {
    List<MenuComponent> menuComponents = new ArrayList<MenuComponent>();
    String name;
    String description;

    public Menu(String name, String description) {
        this.name = name;
        this.description = description;
    }

    public void add(MenuComponent menuComponent) {
        menuComponents.add(menuComponent);
    }

    public void remove(MenuComponent menuComponent) {
        menuComponents.remove(menuComponent);
    }

    public MenuComponent getChild(int i) {
        return menuComponents.get(i);
    }

    public String getName() {
        return name;
    }

    public String getDescription() {
        return description;
    }

    @Override
    public void print() {
        System.out.println();
        System.out.println();
        for (MenuComponent menuComponent : menuComponents) {
            System.out.println(menuComponent.print(););
        }
    }
}
```


```java
class Waitress {
    MenuComponent allMenus;
    
    public Waitress(MenuComponent allMenus) {
        this.allMenus = allMenus;
    } 
    
    public void printMenu() {
        allMenus.print();
    }
}

```

##### client

```java
class MenuTestDrive {
    public static void main(String[] args) {
        MenuComponent pancakeHouseMenu = new Menu();
        MenuComponent dinerMenu = new Menu();
        MenuComponent cafeMenu = new Menu();
        MenuComponent dessertMenu = Menu();
        
        MenuComponent allMenu = new Menu();
        
        allMenu.add(pancakeHouseMenu);
        allMenu.add(dinerMenu);
        allMenu.add(cafeMenu);
        allMenu.add(dessertMenu);
        
        dinerMenu.add(new MenuItem());
        dessertMenu.add(new MenuItem());
        
        Waitress waitress = new Waitress(allMenu);
        waitress.printMenu();
    }
}
```
##### 출처: 헤드 퍼스트 디자인 패텅: 개정판
