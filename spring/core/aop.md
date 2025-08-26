# aop 동작 원리
## 실제 개념
AOP는 관점 지향 프로그래밍을 지원하는 스프링의 기술로, 주로 프록시 패턴을 기반으로 동작합니다.
이를 통해 비즈니스 로직과 별개인 특정 관심사 (로깅, 트랜젝션 처리, 보안, 성능 측정 등)을 분리하여 코드를 깔끔하고 유지보수하기 쉽게 관리할 수 있습니다.
AOP는 기존의 OOP가 클래스 단위로 책임을 나누는 방식에 더해, 횡단 관심사를 별도의 클래스로 분리하는 개념입니다.

### AOP 동작 원리
스프링 aop는 프록시 객체를 생서앟여 기존 객체를 감싸고, 매서드 호출 전후에 공통 기능을 삽입하는 방식으로 동작합니다.
실제로는 두가지 방식으로 프록시를 생성합니다.
1. JDK 동적 프록시
    * 대상 객체가 인터페이스를 구현하고 있을 경우 사용합니다.
    * 프록시 객체가 인터페이스를 구현하며, 내부에 실제 객체를 필드로 보유해 매서드 호출 전후에 필요한 기능(Advice)를 수행합니다. 
1. CGLIB 프록시
    * 대상 객체가 인터페이스를 구현하지 않았을 경우 사용합니다.
    * CGLIB 라이브러리를 사용하여 대상 클래스를 상속한 서브 클래스를 동적으로 생성 후 매서드를 오버라이드 하고, 매서드 호출 전후에 공통 기능을 삽입합니다.
    * 단, final 클래스나 final 매서드는 상속이나 오버라이딩이 불가능하기 때문에 CGLIB 프록시 적용이 불가하므로 주의해야 합니다.

### Transactional
`@Transactional` 또한 스프링 aop를 기반으로 동작하는 트랜젝션 처리 기능입니다.
즉, `@Transactional`을 붙이면, 실제로는 프록시 객체를 통해 트랜젝션 시작 -> 매서드 실행 -> 커밋/롤백이 자동으로 처리됩니다.
다음 객체들로 작동합니다. 
* 프록시 객체: `@Transactional`이 붙은 빈을 빈을 감싸는 객체 (JDK 또는 CGLIB 로 생성)
* `TransactionInterceptor`가 advice 역할을 하여, 트랜젝션 시작/종료 처리합니다.
* `PlatformTransactionManager`이 실제 트랜젝션의 시작/커밋/롤백을 수행합니다.

#### 내부 매서드 호출 -> 외부로 분리해야 합니다.
```java
@Service
public class MyService {
  public void outer() {
    inner(); // 프록시를 거치지 않기 때문에, 트랜젝션이 적용되지 않습니다. 
  }

  @Transactional
  public void inner() {
  }
}
```
#### `@Transactional`에 private 매서드 -> public 으로 변경해야 합니다.
```java
@Transactional
private void saveData() {
  // 프록시는 public 매서드에만 적용하기 때문에, 트랜젝션이 적용되지 않습니다.
}
```
#### 예외가 try-catch로 잡힘 -> 예외를 다시 throw 하거나, 설정을 변경해야 합니다. 
```java
@Transactional
public void doSomething() {
  try {
    throw new RuntimeException("error");
  } catch (Exception e) {
    // RuntimeException 발생할 경우를 삼켜버려, @Transaction에서 롤백이 되지 않습니다. 
  }
}
```
## 내가 이해한 바
## 개발할 때 신경쓰는 점
## 개선된 점
