# entity란
## 실제 개념
엔티티란, DB의 테이블에 매핑되는 자바 클래스입니다. 
* DB의 한 행은 하나의 엔티티 인스턴스에 매핑됩니다.
* 영속성 컨텍스트에 들어가야만 엔티티는 JPA의 다양한 기능(1차 캐시, 변경 감지, 지연 로딩 등)을 지원받을 수 있습니다.
    * 이를 통해 상태, 생명주기, 동일성을 보장받을 수 있습니다. 
### 기본 요건
* `@Entity`를 꼭 클래스 선언부에 붙여야 합니다.
* 기본키에 `@Id` 를 붙여야 합니다.
* 기본 생성자가 있어야 합니다.
* fianl 클래스, 매서드, 필드는 프록시 생성과 변경 감지에 제약이 있으므로 사용을 지양합니다.

### 생명주기
JPA에서 엔티티는 영속성 컨텍스트와의 관계에 따라 4가지의 상태를 가집니다. 
1. 비영속 (Transient)
    * `new`로 생성한 자바 객체는 JPA와 전혀 연관되어있지 않습니다.
    * 따라서 DB에 저장되지도 않습니다.
    ```java
    Member member = new Member("shu");
    ```
1. 영속 (Persistent)
    * `EntityManager.persist(entity)` 호출 시 영속성 컨텍스트에 등록됩니다.
    * 영속성컨텍스트에 등록되면 1차 캐시 관리, 변경 감지, 지연 로딩 등 JPA의 기능을 사용할 수 있습니다.
    ```java
    em.persist(member);
    ```
1. 준영속 (Detached) 
    * 영속 상태에서 영속성 컨텍스트와 분리된 상태로, 더이상 변경 감지가 등작하지 않습니다.
    * `em.detach(entity)`, `em.clear()`, `em.close()` 등으로 준영속 상태가 될 수 있습니다.
    ```java
    em.detach(member);
    ```
1. 삭제 (Removed)
    * `EntityManager.remove(entity)` 호출 시 해당 엔티티가 삭제 예약됩니다.
    * 트랜젝션 커밋 혹은 flush 시점에 `DELETE SQL`이 실행됩니다.
    ```java
    em.remove(member);
    ``` 
