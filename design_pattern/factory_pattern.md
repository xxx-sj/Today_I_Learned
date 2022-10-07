# factory_pattern [팩토리 패턴]

### type(구분)을 통해 하나의 인스턴스를 만들어 낼 때   

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



##### 출처: 헤드퍼스트 디자인 패턴:개정판
