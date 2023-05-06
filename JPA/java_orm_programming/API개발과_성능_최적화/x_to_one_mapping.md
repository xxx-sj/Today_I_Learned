# XToOne 매핑관련

entity를 직접 반환하면 안된다는 것은 기본으로 두고나서, Order를 반환하는데, order class는 아래와 같다.

```java
package jpaBook.jpaShop.domain;

import lombok.AccessLevel;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

import javax.persistence.*;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.List;

@Entity
@Table(name = "orders")
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Getter @Setter
public class Order {

    @Id @GeneratedValue
    @Column(name = "order_id")
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "member_id")
    private Member member;

    //persist 전파
    @OneToMany(mappedBy = "order", cascade = CascadeType.ALL)
    private List<OrderItem> orderItems = new ArrayList<>();

    //oderItemA
    //oderItemB
    //oderItemC
    //persist
    //order persist

    //order persist
    //[생략..]

    @OneToOne(fetch = FetchType.LAZY, cascade = CascadeType.ALL)
    @JoinColumn(name = "delivery_id")
    private Delivery delivery;

    private LocalDateTime orderDate;

    @Enumerated(EnumType.STRING)
    private OrderStatus status; //주문 상태 [order, cancel]

    //==연관관계 메서드 ==//
    // 연관관계 위치는 핵심적으로 컨트롤 하는 쪽에 작성
    public void setMember(Member member) {
        this.member = member;
        member.getOrders().add(this);
    }

    public void addOrderItem(OrderItem orderItem) {
        this.orderItems.add(orderItem);
        orderItem.setOrder(this);
    }

    public void setDelivery(Delivery delivery) {
        this.delivery = delivery;
        delivery.setOrder(this);
    }

    //== 생성 메서드 ==
    public static Order createOrder(Member member, Delivery delivery, OrderItem... orderItems) {
        Order order = new Order();
        order.setMember(member);
        order.setDelivery(delivery);

        for (OrderItem orderItem : orderItems) {
            order.addOrderItem(orderItem);
        }
        order.setStatus(OrderStatus.ORDER);
        order.setOrderDate(LocalDateTime.now());

        return order;
    }

    //== 비즈니스 로직==//
    /**
     * 주문 취소
     */
    public void cancel() {
        if (delivery.getStatus() == DeliveryStatus.COMP) {
            throw new IllegalStateException("이미 배송된 상품은 취소가 불가능합니다.");
        }

        this.setStatus(OrderStatus.CANCEL);
        for (OrderItem orderItem : orderItems) {
            orderItem.cancel();
        }
    }

    //==조회 로직==//

    /**
     * 전체 주문 가격 조회
     * @return
     */
    public int getTotalPrice() {
        return orderItems.stream()
                .mapToInt(OrderItem::getTotalPrice)
                .sum();
    }
}
```
각각의 필드 member와 orderItems, delivery에는 양방향연관관계롤 묶여 있는데 order entity를 그대로 반환하면     
양방향 연관관계에 의해 무한루프에 빠지게된다. 따라서 해결방법은 한쪽에는 @JsonIgnore annotation을 추가해줘야 한다.     


두 번째 문제로는 member는 현재 LAZY LOADING으로 되어있는데, member 객체는 order를 조회하는 시점에는 proxy의 객체가 담겨져 있다가, 
직접 member를 건드릴 때 select를 통해 값을 가져오게 된다.    
하지만 order를 직접 조회해서 json 변환 시 member는 proxy 객체이므로 lib에서 해당 값을 변환하지 못하고 오류를 뱉는다.     
해결방법은 
```gradle
gradle에 추가해주기 
추가해주면 LAZY는 기본으로 null로 반환한다.
	implementation 'com.fasterxml.jackson.datatype:jackson-datatype-hibernate5'
```
추가해주는 방법이 있고, 강제 로딩을 통해 실제객체를 가져오는법     
```java
    @GetMapping("/api/v1/simple-orders")
    public List<Order> ordersV1() {
        List<Order> all = orderRepository.findAllByString(new OrderSearch());
        for (Order order : all) {
            order.getMember().getName(); // LAZY 강제 초기화
	    order.getDelivery().getAddress();
        }
        return all;
    }
```
getMember() 까지만 하면 proxy객체인 상태이고 실제 객체에서 필드값을 가져올 때 실제 객체가 된다. [아무 필드상관없이 ]

**처음부터 entity 반환이 아닌 dto를 만들어 반환 할 것**

##### 출처: https://www.inflearn.com/course/lecture?courseSlug=%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-JPA-API%EA%B0%9C%EB%B0%9C-%EC%84%B1%EB%8A%A5%EC%B5%9C%EC%A0%81%ED%99%94&unitId=24325&category=questionDetail
