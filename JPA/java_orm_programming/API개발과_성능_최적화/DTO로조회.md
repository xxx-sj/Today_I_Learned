# DTO로 조회하기

기존에는 entity를 조회한 후에 DTO에 값을 설정했다면 DTO로 직접 쿼리를 할 수 있다.     
쿼리에 사용되는 dto이다.

```java
package jpaBook.jpaShop.repository;

import jpaBook.jpaShop.domain.Address;
import jpaBook.jpaShop.domain.Order;
import jpaBook.jpaShop.domain.OrderStatus;
import lombok.Data;

import java.time.LocalDateTime;

@Data
public class OrderSimpleQueryDto {

    private Long orderId;
    private String name;
    private LocalDateTime orderDate;
    private OrderStatus orderStatus;
    private Address address;

    public OrderSimpleQueryDto(Long orderId, String name, LocalDateTime orderDate, OrderStatus orderStatus, Address address) {
        this.orderId = orderId;
        this.name = name;
        this.orderDate = orderDate;
        this.orderStatus = orderStatus;
        this.address = address;
    }
}
```
해당 dto를 이용해 repository에서 query를 짠다.
```java
public List<OrderSimpleQueryDto> findOrderDtos() {
    return em.createQuery(
            "select new jpaBook.jpaShop.repository.OrderSimpleQueryDto(o.id, m.name, o.orderDate, o.status, d.address) " +
                    " from Order o" +
                    " join o.member m" +
                    " join o.delivery d", OrderSimpleQueryDto.class)
            .getResultList();
}
```
select에서 new package + class에 생성자를 이용하여 쿼리한 후에 값을 설정한다.    
dto같은 경우 재사용성이 떨어지기 때문에 되도록 entity를 사용하여 조회 [ fetch ] 후 dto를 설정하는 것이 좋다.

##### 출처: https://www.inflearn.com/course/lecture?courseSlug=%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-JPA-API%EA%B0%9C%EB%B0%9C-%EC%84%B1%EB%8A%A5%EC%B5%9C%EC%A0%81%ED%99%94&unitId=24328&category=questionDetail&tab=curriculum

