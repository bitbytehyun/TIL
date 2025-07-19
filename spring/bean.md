# 빈 관리

## 실제 개념
### 빈 생명주기
스프링 컨테이너가 관리하는 객체, 즉 빈은 다음과 같은 생명주기를 가집니다. 
생성 -> 의존성 주입 -> 초기화 -> 사용 -> 소멸
이 과정을 소프트웨어 관점에서 더 상세히 나누면 다음과 같습니다. 

1. 클래스 탐색 및 빈 정의 확인
    * 스프링 컨테이너가 클래스들을 스캔하여 빈으로 등록할 대상을 찾고, 빈 설정 정보를 확인합니다.
1. 객체 생성
    * 빈 클래스의 생성자가 호출되어 인스턴스가 만들어집니다.
1. 의존성 주입
    * @Autowired나 생성자 주입 등을 통해 필요한 의존 객체들이 주입됩니다.
1. 초기화 콜백 호출
    * @PostConstruct 어노테이션이 붙은 메서드, 혹은 InitializingBean.afterPropertiesSet() 메서드가 호출되어 초기화 작업을 수행합니다.
1. 빈 사용
    * 애플리케이션에서 일반 메서드를 호출하는 등 빈이 실제 기능을 수행합니다.
1. 소멸 콜백 호출
    * 컨테이너가 종료되거나 빈이 제거될 때, @PreDestroy 어노테이션이 붙은 메서드, 혹은 DisposableBean.destroy() 메서드가 호출되어 자원 해제 등의 작업을 수행합니다.

* 콜백이란,
  * 함수를 다른 함수의 인자로 전달해서, 특정 시점에 호출되도록 하는 함수입니다.
  * 비동기 작업 처리 (시간이 오래 걸리는 작업이 끝난 후, 실행되는 코드 지정 가능) 또는 유연한 코드를 작성(함수 안에서 어떤 작업을 나중에 해야할 지 외부에서 결정 가능)하고 싶을 때 유용합니다.
  * 스프링에서의 콜백은 특정 이벤트나 상태 변화 시, 프레임워크가 자동으로 호출해주는 개발자 정의 매서드입니다.
    * 객체 초기화 - `InitializingBean`과 `@PostConstruct`
      * 외부 리소스 열기/닫기
      * 캐시 초기화
      * 로그 시작/종료 출력
    * 트랜젝션 - TransactionTemplate에서 doInTransactionWithoutResult 콜백
      * 명시적 트랜젝션 필요
      * rollback 처리가 필요한 경우
    * jdbc - JdbcTemplate에서 RowMapper 콜백
      * sql 결과를 java 객체로 매핑
      * 복잡한 db 결과를 직접 처리 
    * aop - 매서드 실행 전/후 또는 예외 발생 시 콜백으로 부가기능 처리
      * 부가 기능 - 로깅, 트랜젝션, 권한 체크, 캐싱, 성능 측정
      ```java
      @Around()
      public Object logExecution(ProceedingJoinPoint joinPoin) throws Throwable {
         System.out.println("매서드 실행 전");
         Object result = joinPoint.proceed();
         System.out.println("매서드 실행 후");
         return result;
      }
      ```
    * 이벤트 처리 - `ApplicationListener`구현 또는 `@EventListener`을 통해 이벤트 발생시 자동 콜백 실행
      * 알림 전송, 로그 저장, 후속 작업 등
      ```java
      @EventListener
      public void handleEvent(MyEvent event) {
         System.out.println("이벤트 발생 시 자동 실행");
      }
      ```
    * Web 요청 처리 - DispatcherServlet이 요청을 받고, 컨트롤러를 콜백처럼 실행하여 응답 내림
      ```java
      @GetMapping("/hello")
      public String sayHello() {
         return "Hello";
      }
      ```

### 빈 생명주기와 스코프의 관계
빈의 생명주기 실행 시점은 빈의 스코프(scope)에 따라 달라집니다.
* Singleton (기본 스코프)
    * 컨테이너가 시작될 때 빈을 생성하고, 애플리케이션 종료 시점에 소멸 콜백이 호출됩니다.
    * 즉, 컨테이너 생명주기와 빈 생명주기가 일치합니다.

* Prototype
    * 빈을 요청할 때마다 새로운 인스턴스를 생성합니다.
    * 하지만 컨테이너는 빈을 생성하고 의존성 주입, 초기화까지만 담당하며, 소멸 콜백은 호출하지 않습니다.
    * 따라서 프로토타입 빈은 직접 소멸 처리를 해야 합니다.

* Request
    * HTTP 요청이 시작될 때 빈을 생성하고, 요청이 끝나면 소멸됩니다.
    * 주로 웹 컨트롤러에서 사용되며, 요청 범위에 따라 생명주기가 관리됩니다.

* Session
    * HTTP 세션 단위로 빈이 생성되고 소멸됩니다. 로그인 사용자 정보 저장 등에 사용합니다.

### 초기화/소멸 콜백
초기화 및 소멸 단계에서는 콜백 기술이 사용됩니다. 
콜백이란, 개발자가 직접 호출하지 않아도 특정 상황에서 시스템(스프링)이 자동으로 호출해주는 함수를 말합니다. 
이는 IoC 개념의 대표적인 구현 방식 중 하나입니다. 

스프링에서는 다음과 같은 콜백 매서드를 제공하여 빈의 생명주기 과정에서 개발자가 특정 작업을 개입할 수 있도록 합니다.

* 초기화 콜백 : DB 커넥션 풀 초기화, 캐시 로딩, 설정값 검증 등에 사용되며, 의존성 주입이 끝난 뒤 자동으로 호출됨
    * `@PostConstruct`
    * `InitializingBean`인터페이스의 `afterPropertiesSet()`
* 소멸 콜백 : 자원 해제, 커넥션 닫기, 쓰레드 종료 등에 사용됨
    * `@PreDestroy`
    * `DisposableBean` 인터페이스의 `destroy()`

스프링에서는 `BeanPostProcessor`가 빈의 생명주기 과정에서 콜백을 실행해주는 핵심 컴포넌트 역할을 합니다. 
`BeanPostProcessor`는 빈이 생성된 직후와 초기화 전후에 개입하여 다음과 같은 작업을 진행합니다. 
* postProcessBeforeInitialization(Object bean, String beanName)
    → @PostConstruct 실행 전 호출

* postProcessAfterInitialization(Object bean, String beanName)
    → 초기화가 끝난 후 호출

## 내가 이해한 바
## 내가 개발할 때 신경쓰는 점
## 개선된 점
