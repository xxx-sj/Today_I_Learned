
```
데이터베이스를 하나만 사용하는 어플리케이셔은 일반적으로 EntityManagerFactory를 하나만 생성한다.
```

```java
EntityManagerFactory emf = Persistance.createEntityManagerFactory(".xml"); // .xml file 데이터베이스 정의된 곳
```

```
factory 생성은 비용이 많이 들기때문에 되도록 하나만 생성하고, 해당 factory에서 필요할 때마다
엔티티 매니저를 생성한다.
```

```java
EntityManager em = emf.createEntityManager();
```

```
factory는 동시에 접근해도 안전하여 서로 다른 스레드 간에 공유가 되지만, 엔티티 매니저는 동시에 접근하면 
동시성 문제가 발생하므로 공유하면 안된다.
엔티티 매니저는 데이터베이스 커넥션이 필요한 시점까지 커넥션을 얻지 않는다.
보통 트랜잭션 시작 시 커넥션을 획득한다.
```


##### 출처: 자바 ORM 표준 JPA 프로그래밍
