# 엔티티 연관관계
## 실제 개념 
JPA에서 엔티티는 서로 참조 관계를 가질 수 있으며, 이는 DB의 외래키(FK)와도 매핑됩니다. 
연관관계 매핑은 단순히 외래키를 매핑하는 것을 넘어서, 객체의 생명주기 관리, 양방향 참조 동기화, 삭제/저장 전이 등을 포함합니다.
### 1. Cascade: 엔티티 생명주기 전이
`cascade` 옵션은 한 엔티티의 영속성 컨텍스트 동작을 연관된 엔티티까지 저이시킬지 여부를 결정합니다. 
즉, 부모 엔티티에서 persist/remove 등을 호출할 때 자식 엔티티에도 같은 동작이 적용됩니다.
#### 종류
* PERSIST : 부모 저장 시 자식도 함께 저장
* MERGE : 부모 병합 시 자식도 함께 병합
* REMOVE : 부모 삭제 시 자식도 함께 삭제
* REFRESH : 부모 새로고침 시 자식도 함께 새로고침
* DETACH : 부모 detach 시 자식도 detach
* ALL : 위 모든 옵션 적용
#### 주의점
* 의도치 않게 엔티티가 저장/삭제될 수 있습니다. (예: Member-Team 관계에서 cascade = ALL 설정 후 member 삭제 시 team까지 삭제)
* 데이터 독립성이 필요한 엔티티에는 사용하지 않아야 합니다.
#### 활용
* 부모-자식이 완전 종속 관계일 때 사용합니다. (예: Order ↔ OrderItem (OrderItem은 Order 없이 존재 불가))
* 읽기 전용, 단순 참조 관계에는 사용하지 않음

```java
@OneToMany(mappedBy = "order", cascade = CascadeType.ALL)
private List<OrderItem> orderItems = new ArrayList<>();
```

### 2. orphanRemoval: 고아 객체 자동 삭제
`orphanRemoval = true`는 부모 엔티티에서 컬렉션에서 자식을 제거하면, 해당 자식을 DB에서 자동 삭제하는 기능입니다.

#### 특징
* 부모와의 연관관계가 끊어진 고아 객체는 DELETE SQL로 DB에 반영
* * CascadeType.REMOVE와 비슷하지만, orphanRemoval은 연관관계 제거 시 동작 (REMOVE는 부모 삭제 시 동작)
#### 활용
* 완전 종속 관계에 적합합니다. (예: Order - OrderItem, Post - Comment)
```java
@OneToMany(mappedBy = "post", orphanRemoval = true)
private List<Comment> comments = new ArrayList<>();
```

### 3. 연관관계 매핑 방식
#### 단방향
```java
@Entity
public class Order {
    @ManyToOne
    @JoinColumn(name = "member_id")
    private Member member;
}
```
* Order에서만 Member를 접근할 수 있습니다. Member에서는 Order를 모릅니다.
* Member -> Order 처럼 반대 방향 조회 시, JPQL이 필요합니다.
#### 양방향
```java
@Entity
public class Order {
    @ManyToOne
    @JoinColumn(name = "member_id")
    private Member member;
}

@Entity
public class Member {
    @OneToMany(mappedBy = "member")
    private List<Order> orders = new ArrayList<>();

    // 연관관계 편의 메서드
    public void addOrder(Order order) {
        orders.add(order);
        order.setMember(this);
    }
}
```
* 양쪽 객체에서 모두 참조 가능해서 조회 쿼리 단순화됩니다.
* 연관관계 주인(외래키를 가진 쪽)만 FK 변경 가능하며, mappedBy는 주인(fk 변경 권한)이 아님
* 편의 메서드 필요합니다.
    * 양방향 참조 동기화는 JPA가 자동으로 하지 않습니다!!
    * 예: member.getOrders().add(order)만 하면 order.member는 null 상태 → 데이터 불일치 가능
* 편의 메서드를 만들어 양쪽 참조를 동시에 설정해야 합니다.
#### 그외 연관관계 설계 주의점
* 다대다(@ManyToMany) : 사용하지 않습니다. (중간 테이블 매핑 불가능, 추가 칼럼 불가, 추적 어려움)
    * 중간 엔티티를 만들어서 일대다 + 다대일로 풀어야 합니다.
* 일대다 단방향 : 외래키가 다른 테이블에 있어 fk 변경시 update 쿼리가 2번 발생할 수 있으므로 사용하지 않습니다. 
