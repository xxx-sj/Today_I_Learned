# 엔티티를 DTO로 변환 - 페이징과 한계 돌파

order [one] 과 orderItem [many] 에서 order [one] 을 기준으로 페이징하는 것이 목적이다.      
그런데 데이터는 다 [many] 를 기준으로 row가 생성된다.    
이 경우 경고 로그와 함께 db 데이터를 읽어서 메모리에서 페이징을 시도한다. [ row 기준으로 ]     

### 한계 돌파

페이징 + 컬렉션 엔티티 조회 문제 [oneToMany] 해결     
- XToOne 관계는 모두 fetch 조인한다. [ oneToOne, ManyToOne ] row를 증가시키지 않는다.

- 컬렉션은 지연로딩으로 조회한다.


- 지연 로딩 성능 최적화를 위해 [ hibernate.default_batch_fetch_size ], @BatchSize 를 적용한다.
  - 이 옵션을 사용하면 컬렉션이나, 프록시 객체를 한꺼번에 설정한 size 만큼 in 쿼리로 조회한다.
```java
#jpa setting
  jpa:
    properties:
      hibernate:
        default_batch_fetch_size: 100
        


public class Order {

  @BatchSize(size = 1000)
  private List<orderItem> orderItems = new ArrayList<>();
}


// XToOne 은 class level에 설정
@BatchSize(size = 100)
public abstract class Item {}
```

이 전에는 orderItem을 가져올 때 where절에 [ order가 2개라고 했을 때 ] order_id = ? 로 총 2번의 쿼리를 보냈었는데,     
hibernate.default_batch_fetch_size 나, @BatchSize를 사용하면 in 절을 통해 where order_id in (? , ?) 로 총 1번의 쿼리가     
나가게 된다. 또한 orderItem에 걸려있는 item도 한번에 4개를 동시에 가져오게 된다.    item_id in (?, ?, ?, ?)
위에서 설정하는 값은 in 절의 갯수를 설정하게 된다. 

ex) row가 1000개고 설정한 값이 100 이라면 10번 query를 날리게 된다.

```
이 전에는 order를 조회하면 2개의 결과가 나오고, order에 속한 orderItem이 각각의 order에 2개씩 조회되었다 collection /
order1 - orderItem1 / orderItem2
order2 - orderItem3 / orderItem4

추가로 orderitem에 속한 item까지 조회하면

order1 - orderItem1 - Item1
       - orderItem2 - Item2
       
order2 - orderItem3 - Item3
       - orderItem4 - Item4

select * from order; -> 결과 row 2개
select * from orderItem where order_id = order1_id -> row 2개 
select * from orderItem where order_id = order2_id -> row 2개
select * from Item where order_item_id = orderItem_id1 -> row 1개
select * from Item where order_item_id = orderItem_id2 -> row 1개
select * from Item where order_item_id = orderItem_id3 -> row 1개
select * from Item where order_item_id = orderItem_id4 -> row 1개



order 조회 1번 / orderItem 2번 [ 각각의 order에서 조회 ] / orderItem 조회 시 2개가 조회되니 루프 돌면서 item 조회 4번
```

이제 위에 설정[batchSize / hibernate.default_batch_fetch_size] 하고 나면,

```
select * from order -> 2개
select * from orderItem where order_id in (order1_id, order2_id) -> row 4개
select * from item where order_item_id in (order_item1_id, order_item2_id, order_item3_id, order_item4_id) -> row 4개
```

와 같이 결과가 나오는데, 쿼리 수를 보면 위에는 7개, 아래는 3개로 쿼리 수의 차이가 나게 된다.       
보면 설정을하게되면 where절에 in query가 나가게 된다.     


ToOne관계는 페치조인으로 하고, 나머지는 hiobernate.default_batch_fetch_size로 최적화 하자 
size는 100 ~ 1000개 정도가 max라고 생각하자. 100이든, 1000이든 전체 데이터를 로딩해야 하므로 메모리 사용량은 같다.    
순간 부하를 어디까지 견딜 수 잇늦리로 결정하면 된다.


##### 출처: https://www.inflearn.com/course/lecture?courseSlug=%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-JPA-API%EA%B0%9C%EB%B0%9C-%EC%84%B1%EB%8A%A5%EC%B5%9C%EC%A0%81%ED%99%94&unitId=24333&category=questionDetail&tab=curriculum

