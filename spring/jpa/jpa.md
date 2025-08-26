# JPA란
## 실제 개념
JPA란 Java의 ORM 표준 명세입니다. 즉, 구현체가 아니라 인터페이스와 규약만 정의해 둔 스펙이며, 이를 동작시키기 위해서는 구현체 (예: Hibernate, EclipseLink 등)이 필요합니다. 
ORM 기술이 자바 개발자들 사이에서 널리 사용되면서, 각 구현체별로 API와 사용 방법이 달라서 호환성과 이식성이 떨어졌습니다. 
이를 해결하기 위해 ORM을 표준화하는 JPA가 정의되었습니다. 

### 구성 요소
1. EntityManagerFacotory
    * 싱글톤으로 애플리케이션 전체에서 하나만 생성
    * EntityManager를 생성하는 역할을 수행합니다.
    * 메타데이터 로딩: `persistene.xml` 또는 스프링 설정을 기반으로 엔티티 매핑 정보, 데이터베이스 연결 설정, SQL Dialect, 캐시 전략 등 JPA 실행에 필요한 모든 메타데이터를 보관합니다.
    * JDBC 연결 자원 관리: DataSource, 커넥션 풀 설정을 유지합니다.
    * 2차 캐시 지원 : 영속성 컨텍스트의 트랜젝션 단위에서 동작하는 1차 캐시와 달리, 어플리케이션 전역에서 저장되는 2차 캐시를 지원합니다. 이는 Ehcache, Redis등으로 구현될 수 있습니다.
        * 종류 : 엔티티 캐시 - `@Cacheable`, 컬렉션 캐시 - `OneToMany`/`@ManyToMany 관계의 컬랙션 결과 캐시, 쿼리 캐시 - JPQL/HQL 쿼리 결과를 캐시
        * 동작 : `em.find(member)`시, 1차 캐시 조회 -> 2차 캐시 조회 -> DB조회 순으로 작동합니다.
        * 활용 : 동기화문제와 메모리 사용량 증가의 문제가 있기 때문에, 자주 변경되지 않는 마스터 데이터(예: 상품 카테고리, 지역명, 코드 테이블 등)에 적합합니다. 
1. EntityManager
    엔티티 매니저란 영속성 컨텍스트를 관리하는 핵심 객체입니다. 이외에도 다음 역할을 수행합니다. 
    * 엔티티의 CRUD 작업 수행
    * JPQL, Criteria, native SQL 실행
    * 영속성 컨텍스트의 변경 사항을 flush하여 DB에 동기화
    * 트랜젝션 경계 내에서 엔티티의 동일성 보장
    ```
    EntityManager
     ├─ PersistenceContext (1차 캐시, 스냅샷, 쓰기 지연 SQL 저장소)
     ├─ JDBC Connection (DataSource 통해 획득)
     └─ Transaction (PlatformTransactionManager 또는 ResourceLocal)
    ```
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
