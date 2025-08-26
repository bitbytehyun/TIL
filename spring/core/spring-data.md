# Spring data 란
## 실제 개념
스프링 데이터(Spring Data)는 여러 데이터 저장소(RDB, NoSQL 등)를 위한 공통 인터페이스와 프로그래밍 모델을 제공하는 프레임워크입니다.

이 중 spring-data-commons라는 공통 모듈이 CrudRepository, PagingAndSortingRepository 같은 공통 인터페이스와 기본 동작을 제공하며,
각 저장소별 세부 구현은 스프링 데이터의 하위 모듈(예: Spring Data JPA, MongoDB, Redis 등)로 분리되어 있습니다. (JpaRepository, MongoRepository 등)

덕분에 개발자는 저장소 종류에 상관없이, 인터페이스만 선언하면 스프링 데이터가 자동으로 구현체를 생성해주어,
findById() 같은 공통 메서드를 편리하게 사용할 수 있습니다.
