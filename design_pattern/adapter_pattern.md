# adapter_pattern [어댑터 패턴]

```
특정 클래스 인터페이스를 클라이언트에서 요구하는 다른 인터페이스로 변환한다.
인터페이스가 호환되지 않아 같이 쓸 수 없었던 클래스를 사용할 수 있게 도와준다.
```

```java
package main.designPattern.adapter;

public class AdapterPattern {
}



interface Duck {
    public void quack();
    public void fly();
}

class MallardDuck implements Duck {

    @Override
    public void quack() {
        System.out.println("괙");
    }

    @Override
    public void fly() {
        System.out.println("날고 있어요");
    }
}

interface Turkey {
    public void gobble();
    public void fly();
}

class WildTurkey implements Turkey {

    @Override
    public void gobble() {
        System.out.println("골골");
    }

    @Override
    public void fly() {
        System.out.println("짧은거리를 날고 있어요");
    }
}

class TurkeyAdapter implements Duck {
    Turkey turkey;

    public TurkeyAdapter(Turkey turkey) {
        this.turkey = turkey;
    }

    public void quack() {
        turkey.gobble();
    }

    //터키는 짧은 거리를 날기 때문에 대응하기 위해 5번 호출
    public void fly() {
        for(int i = 0; i < 5; i++) {
            turkey.fly();
        }
    }
}


//class adapter implements Target {
//    Adaptee adaptee;
//
//    public adapter(Adaptee adaptee) {
//        this.adaptee = adaptee;
//    }
//
//    public void request() {
//        this.adaptee.specificRequest();
//    }
//}


class DuckTestDrive {
    public static void main(String[] args) {
        Duck duck = new MallardDuck();

        Turkey turkey = new WildTurkey();
        Duck turkeyAdapter = new TurkeyAdapter(turkey);


    }
}
```



```
Enumeration을 iterator에 적응시키고 있기에 어댑터는 iterator 인터페이스를 구현해야한다.
밖에서 봤을 때 해당 어댑터는 iterator처럼 보여야 하기 때문이다.


적응시켜야 하는 enumeration객체. 구성을활용하고 있기에 인스턴스 변수에 저장한다.

iteraotr의 hasNext() 메소드는 Enumeration의 hasMoreElement() 메소드로 연결된다.
iteraotr의 next메소드는 enumberation의 nextElment()로 연결된다.

remote는 지원지되 않으므로 포기하여 예외를 던진다.

target과 adaptee의 메소드가 1:1로 대응되지 않는 상황에서는 어댑터를 완벽하게 적용할 수 없다.
클라이언트는 예외 발생 가능성을 염두에 두고 있어야 하니까.



adapter = EnumberationIterator
adaptee = enumberation
target = iterator

```


```java

class EnumerationIterator implements Iterator<Object> {
    Enumeration<?> enumeration;
    
    public EnumerationIterator(Enumeration<?> enumeration) {
        this.enumeration = enumeration;
    }
    
    public boolean hasNext() {
        enumeration.hasMoreElements();
    }
    
    public Object next() {
        return enumeration.nextElement();
    }
    
    public void remove() {
        throw new UnsupportedOperationException();
    }
}

```



###### 출처: 헤드퍼스트 디자인패턴: 개정판
