# ioc와 di란
## 실제 개념
IoC(Inversion of Control)는 프로그램의 제어권을 개발자가 아닌 시스템 또는 프레임워크가 갖는 현대 소프트웨어 아키텍처의 핵심 개념입니다.
제워권은  객체 생성, 실행 흐름 제어, 객체의 생명주기(Lifecycle) 제어, 의존성 주입(Dependency Injection) 등이 될 수 있습니다.
IoC는 다양한 방식으로 구현될 수 있습니다. 예를 들어:
* 의존성 주입(DI): 객체 생성과 연결을 외부에서 주입함
* 콜백/이벤트: 흐름을 외부에서 호출하도록 등록
* 템플릿 메서드 패턴: 상위 클래스가 전체 흐름을 결정하고, 하위 클래스는 구체 구현
* 전략 패턴: 알고리즘을 외부에서 주입하여 유연하게 바꿈


이 중에서 Spring이 IoC를 실현하는 핵심 기술이 바로 DI(Dependency Injection)입니다.
DI는 의존 객체를 직접 new로 생성하지 않고, 프레임워크가 알아서 주입해주는 방식입니다.

만약 개발자가 직접 객체를 생성한다면 다음과 같은 문제가 생깁니다:
```java
public class OrderService {
  private OrderRepository repo = new OrderRepository(); // 직접 생성
}
```
* 구현체를 바꾸려면 코드를 수정해야 함
* 테스트 시 Mock 객체를 주입하기 어려움
* 다양한 구현체를 유연하게 연결하기 어려움 

DI를 사용하면 외부에서 객체를 주입받아 유연하고 테스트 가능한 구조가 됩니다:
```java
@Component
public class OrderService {
  private final OrderRepository repo;

  @Autowired // 생략 가능
  public OrderService(OrderRepository repo) {
    this.repo = repo;
  }
}
```
이처럼 생성자 주입을 사용하면:
* final로 불변성 유지 가능
* 순환 참조 탐지가 쉬움
* 테스트 시 가짜 객체 주입이 쉬움
따라서 스프링에서는 생성자 주입을 가장 권장합니다.

주입 방식은 다음과 같은 3가지가 있습니다:
1. 생성자 주입 (권장)
1. 세터 주입 (@Autowired를 세터 메서드에 부착)
  ```java
  @Component
  public class OrderService {
   private OrderRepository orderRepository;
   @Autowired
   public void setOrderRepository(OrderRepository orderRepository) {
     this.orderRepository = orderRepository;
   }
  }
  ```
1. 필드 주입 (@Autowired 바로 필드에 부착) – 편리하지만 테스트/유지보수에 불리하여 실무에서는 비추천
  ```java
  @Component
  public class OrderService {
    @Autowired
    private final OrderRepository orderRepository;
  }
  ```


## 내가 이해한 바
### 생성자 주입을 기본으로 사용합니다. 
- 불변성을 유지할 수 있고,
- 의존성 누락 시 컴파일 시점 또는 컨테이너 초기화 시점에 오류 확인이 가능하고,
- 단위 테스트에서 필요한 객체만 주입할 수 있어 테스트 작성이 쉬워집니다.
### 테스트 가능한 구조로 설계할 수 있습니다.
DI를 적용하면 테스트 시 구현체 대신 Mock 객체를 쉽게 주입할 수 있어서, 단위테스트가 쉬워집니다.
```java
OrderRepository mockRepo = mock(OrderRepository.class);
OrderService orderService = new OrderService(mockRepo);
```
### 스프링 컨테이너의 IoC 이해
- 객체의 생성 시점, 생명 주기, 중비 시점 등을 컨테이너가 관리함을 이해하고 코드를 작성합니다.

## 내가 개발할 때 신경쓰는 점
## 개선된 점
