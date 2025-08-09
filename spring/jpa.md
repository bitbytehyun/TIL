# JPA란
## 실제 개념
JPA란 Java의 ORM 표준 명세입니다. 즉, 구현체가 아니라 인터페이스와 규약만 정의해 둔 스펙이며, 이를 동작시키기 위해서는 구현체 (예: Hibernate, EclipseLink 등)이 필요합니다. 
ORM 기술이 자바 개발자들 사이에서 널리 사용되면서, 각 구현체별로 API와 사용 방법이 달라서 호환성과 이식성이 떨어졌습니다. 
이를 해결하기 위해 ORM을 표준화하는 JPA가 정의되었습니다. 

### 구성 요소
1. EntityManagerFacotory
    * 싱글톤으로 애플리케이션 전체에서 하나만 생성
    * EntityManager를 생성하는 역할을 수행합니다. 
1. EntityManager
    * 트랜젝션 단위의 작업을 관리하고, 영속성 컨택스트를 생성 및 관리합니다.
    * 엔티티의 crud, jpql 실행, flush/clear 호출 등 DB 작업을 수행합니다. 
    * 스레드 세이프하지 않아서 요청마다 별도의 인스턴스를 사용해야 합니다.  
1. 영속성 컨텍스트 (Persistence Context)
    * EntityManager가 관리하는 엔티티 저장소입니다.
    * 엔티티 생명주기를 관리합니다 : 비영속 -> 영속 -> 준영속 -> 삭제
    * 1차 캐시
    * 변경 감지
    * 지연 로딩

```java
EntityManager em = emf.createEntityManager();
EntityTransaction  tx = em.getTransaction();
tx.begin();

Member member = new Member("shu");
em.persist(member); // 1차 캐시에 저장 : insert SQL은 바로 실행되지 않고 쓰기 지연 저장소에 저장됨

tx.commit(); // 영속성 컨텍스트의 종료. flush되고, SQL 실행됨
em.close();
```
