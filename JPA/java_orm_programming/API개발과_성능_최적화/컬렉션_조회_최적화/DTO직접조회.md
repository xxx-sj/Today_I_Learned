# DTO 직접조회

DTO를 이용하여 직접 조회하는 법 entity -> dto [x] / dto

query select절에 직접 dto를 new 하여 설정하는 법

일반적인 entity조회와 분리하기위해 package를 나눈다. 나눌 때는 repository 밑에 
order.query로 두었다.

- 첫 번째로 order를 dto로 받는다.
```java
public List<OrderQueryDto> findOrderQueryDtos() {
    List<OrderQueryDto> result = findOrders();

    result.forEach(o -> {
        List<OrderItemQueryDto> orderItems = findOrderItems(o.getOrderId());
        o.setOrderItems(orderItems);
    });

    return result;
}

private List<OrderQueryDto> findOrders() {
    return em.createQuery(
            "select new jpaBook.jpaShop.repository.order.query.OrderQueryDto(o.id, m.name, o.orderDate, o.status, d.address)" +
                    " from Order o" +
                    " join o.member m" +
                    " join o.delivery d", OrderQueryDto.class)
            .getResultList();
}



///////////////////// OrderQueryDtos
package jpaBook.jpaShop.repository.order.query;


@Data
public class OrderQueryDto {

    private Long orderId;
    private String name;
    private LocalDateTime orderDate;
    private OrderStatus orderStatus;
    private Address address;
    private List<OrderItemQueryDto> orderItems;

    public OrderQueryDto(Long orderId, String name, LocalDateTime orderDate, OrderStatus orderStatus, Address address) {
        this.orderId = orderId;
        this.name = name;
        this.orderDate = orderDate;
        this.orderStatus = orderStatus;
        this.address = address;
//        this.orderItems = orderItems;
    }

}
```

다음으로 collection 부분을 따로 조회하여 set OrderQueryDto에 set 해준다.

```java
package jpaBook.jpaShop.repository.order.query;

@Repository
@RequiredArgsConstructor
public class OrderQueryRepository {

    private final EntityManager em;


    public List<OrderQueryDto> findOrderQueryDtos() {
        List<OrderQueryDto> result = findOrders();

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
        result.forEach(o -> {
            List<OrderItemQueryDto> orderItems = findOrderItems(o.getOrderId());
            o.setOrderItems(orderItems);
        });

        return result;
    }
///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
    private List<OrderItemQueryDto> findOrderItems(Long orderId) {
        return em.createQuery(
                "select new jpaBook.jpaShop.repository.order.query.OrderItemQueryDto(oi.order.id, i.name, oi.orderPrice, oi.count)" +
                        " from OrderItem oi" +
                        " join oi.item i" +
                        " where oi.order.id = :orderId", OrderItemQueryDto.class)
                .setParameter("orderId", orderId)
                .getResultList();

    }
///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

    private List<OrderQueryDto> findOrders() {
        return em.createQuery(
                "select new jpaBook.jpaShop.repository.order.query.OrderQueryDto(o.id, m.name, o.orderDate, o.status, d.address)" +
                        " from Order o" +
                        " join o.member m" +
                        " join o.delivery d", OrderQueryDto.class)
                .getResultList();
    }
}

////////////////////////////////////////////////////////////////OrederItemQueryDto

package jpaBook.jpaShop.repository.order.query;


@Data
public class OrderItemQueryDto {

    private Long orderId;
    private String itemName;
    private int orderPrice;
    private int count;

    public OrderItemQueryDto(Long orderId, String itemName, int orderPrice, int count) {
        this.orderId = orderId;
        this.itemName = itemName;
        this.orderPrice = orderPrice;
        this.count = count;
    }
}
```

ToOne 관계는 row수가 증가하지 않지만 
ToMany관계는 조인하면 row수가 증가한다.    
이 것 또한 n+1 문제가 발생한다.

##### 출처: https://www.inflearn.com/course/lecture?courseSlug=%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-JPA-API%EA%B0%9C%EB%B0%9C-%EC%84%B1%EB%8A%A5%EC%B5%9C%EC%A0%81%ED%99%94&unitId=24334&category=questionDetail&tab=curriculum
