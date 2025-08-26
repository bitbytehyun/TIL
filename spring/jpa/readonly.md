# `@Transactional(readonly = true)` 설정이란
## 실제 개념
`@Transactional` 어노테이션에 설정할 수 있는 추가 부가 기능 중 하나로, 해당 트랜젝션이 읽기 전용임을 선언하는 설정입니다. 
스프링은 내부적으로 트랜젝션 메니저에게 일긱 전용 트랜젝션임을 전달하고, 그 결과는 사용하는 트랜젝션 메니저의 구현체에 따라 동작 방식이 달라집니다. 

### 1. `DataSourceTransactionManager` (JDBC 기반)
JDBC의 `Connection` 객체에 `setReadOnly(true)`를 호출하여, DBMS 옵티마이저에 읽기 전용 트랜젝션임을 알리는 힌트로 전달됩니다. 
일부 데이터베이스에서는 락을 덜 걸거나, 불필요한 undo/redo 로그 생성을 생략하는 등 성능 최적화가 적용됩니다. 

### 2. `JpaTransactionManager` (JPA 기반)
내부의 영속성 컨텍스트에게 읽기 전용임을 알립니다. 
이 설정으로 인해, `flush()`호출이 생략되어 DB에 변경사항이 반영되지 않고, dirty checking도 수행되지 않습니다. 
즉, 조회만 수행할 때 불필요한 처리 비용이 줄고, 실수로 데이터 변경하는 것도 방지할 수 있습니다. 

### 활용
* 대용량 조회 처리 시 성능 최적화가 필요할 때
* 읽기 전용 매서드에서 실수로 데이터 변경을 막고 싶을 때
* 트랜젝션 안에서 update/insert 없는 단순 read 매서드일 때
```java
@Transactional(readOnly = true)
public List<Port> getAllPosts() {
  return postRepository.findAll();
}
```
