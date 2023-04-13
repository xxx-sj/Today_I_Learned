# 영속성 전이(CASCAED)와 고아객체

특정 엔티티를 영속 상태로 만들 때 연관된 엔티티도 함께 영속 상태로 만들고 싶을 때     
부모 엔티티를 저장할 때 자식 엔티티도 함께 저장     


```java

class Parent {

  ...
  
  
  @OneToMany(mappedBy = "parent")
  private List<Child> childList = new ArrayList();
  
  
  public void addChild (Child child) {
    this.childList.add(child);
    child.setParent(this);
  }
  
  
}


class Child {

  ...
  
  @ManyToOne
  @JoinColumn(name = "parent_id")
  private Parent parent;
}
```

데이터를 저장하기 위해 부모 엔티티 한개에 자식 엔티티 2개를 생성하면 3개 모두 초기화를 위해   
**em.persist()**를 총 3번을 해줘야 한다.     

parent한번의 저장으로 모두 하고 싶다면, *cascade = Cascade.Type = ALL** 을 주면 된다.

```java
class Parent {

  ...
  
  
  @OneToMany(mappedBy = "parent", cascade = CascadeType.ALL)
  private List<Child> childList = new ArrayList();
  
  
  public void addChild (Child child) {
    this.childList.add(child);
    child.setParent(this);
  } 
}
```
Collection 내에 저장된 엔티티도 persist를 해준다.      

만약 child를 다른 enttiy에서 사용한다면 사용하지 않는것이 좋다. 

* * *

## 고아객체

- 고아 객체 제거: 부모 엔티티와 연관관계가 끊어진 자식 엔티티를 자동으로 삭제
- orphanRemoval = true
- parent.getChildList().remove(0); -> DELETE FROM ...

```java

class Parent {

  ...
  
  
  @OneToMany(mappedBy = "parent", orphanRemoval = true)
  private List<Child> childList = new ArrayList();
  
  
  public void addChild (Child child) {
    this.childList.add(child);
    child.setParent(this);
  } 
}

```

참조하는 곳이 하나일 때 사용할 것 [게시판 첨부파일]
특정 엔티티가 개인 소유할 때 사용
@OneToOne, @OneToMany만 가능  [부모 객체에서 사용가능하기 때문에]


** cascade = CascadeType.ALL, orphanRemoval = true ** 를 같이 사용하게 되면 parent에서 child의 life cycle을     
관리하게 된다.




##### 출처: https://www.inflearn.com/course/lecture?courseSlug=ORM-JPA-Basic&unitId=21710&tab=curriculum
