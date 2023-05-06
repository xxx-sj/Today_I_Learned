# 엔티티를 DTO로 변환

DTO로 변환해서 반환을 할 때도 내부에 연관된 DTO 또한 반환해서는 안된다.

```java
@Data
static class OrderDto {

    private Long orderId;
    private String name;
    private LocalDateTime orderDate;
    private OrderStatus orderStatus;
    private Address address;
    private List<OrderItem> orderItems;

    public OrderDto(Order order) {
        orderId = order.getId();
        name = order.getMember().getName();
        orderDate = order.getOrderDate();
        orderStatus = order.getStatus();
        address = order.getDelivery().getAddress();
        order.getOrderItems().stream().forEach(o -> o.getItem().getName());
        orderItems = order.getOrderItems();
    }
}
```
여기서 orderItems를 반환하는데 OrderItem 또한 ENTITY로 그대로 반환하여 모두 노출해서는 안된다.    
orderItem도 DTO로 변환해서 반환해야 한다. DTO로 변환할 것

```java

@Data
static class OrderDto {

    private Long orderId;
    private String name;
    private LocalDateTime orderDate;
    private OrderStatus orderStatus;
    private Address address;
    private List<OrderItemDto> orderItems;

    public OrderDto(Order order) {
        orderId = order.getId();
        name = order.getMember().getName();
        orderDate = order.getOrderDate();
        orderStatus = order.getStatus();
        address = order.getDelivery().getAddress();
        orderItems = order.getOrderItems().stream()
                .map(orderItem -> new OrderItemDto(orderItem))
                .collect(Collectors.toList());
    }
}

@Data
static class OrderItemDto {

    private String itemName;
    private int orderPrice;
    private int count;

    public OrderItemDto(OrderItem orderItem) {
        this.itemName = orderItem.getItem().getName();
        this.orderPrice = orderItem.getOrderPrice();
        this.count = orderItem.getCount();
    }
}
```
반환할 때는 객체 배열로 반환보다는 한번 더 감싸는 result 객체를 생성하여 내부 data에 포함시키는 것이 좋다.    
**embeded / value object는 가능**

이렇게되면 쿼리가 모두 order가 2개인 것을 기준으로 
order 2개 조회 1번
orderDto에서 member 조회 1번 member.getName()
배송조회 1번 delivery
orderItems 1번 조회 [ 조회 갯수 2번 ]
orderItems 에서 item 1번씩 
해서 여러번 나가게 된다.



##### 출처: https://www.inflearn.com/course/lecture?courseSlug=%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-JPA-API%EA%B0%9C%EB%B0%9C-%EC%84%B1%EB%8A%A5%EC%B5%9C%EC%A0%81%ED%99%94&unitId=24331&category=questionDetail&tab=curriculum
