# interceptor란
## 실제 개념
인터셉터란 클라이언트 요청이 컨트롤러에 도달하기 전후에 요청을 가로채서 필요한 작업을 수행하는 컴포넌트입니다. 
즉, 요청과 응답을 중간에서 가로채어 중간자 역할을 합니다.
주요 기능은 다음과 같습니다 
* 요청 전 처리 (예: 인증 체크, 권한 확인, 요청 파라미터 가공, 로깅 등)
* 컨트롤러 실행 후 처리 (예: 응답 데이터 조작, 후처리 로그 등)
* 뷰가 랜더링 된 후 처리 (예: 리소스 정리, 성능 모니터링 종료 등)

(자바에서는 그럼 인터셉터 개념이 없겠네? 왜냐면 서블릿에서는 직접 mvc패턴 없이 다 구현하는거니까 컨트롤러 가기 전 단계도 없는거지 )
### 스프링에서의 interceptor
스프링 mvc에서는 `HandlerInterceptor` 인터페이스를 구현하여 만듭니다. 
주로 `preHandle()`, `postHandle()`, `afterCompletion()` 매서드를 오버라이드 하여 사용합니다. 
* `preHandle()`: 컨트롤러 호출 전에 실행
* `postHandle()`: 컨트롤러 시랳ㅇ 후, 뷰 랜더링 전에 실행
* `afterCompletion()`: 뷰 랜더링 완료 후 실행

스프링 설정 클래스에서 `WebConfigurer`를 구현하여 `addInterceptors()` 매서드에 등록할 수 있습니다. 
```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
  @Override
  public void addInterceptors(InterceptorRegistry registry) {
    registery.addInterceptor(new MyInterceptor())
      .addPathPatterns("/user/**")
      .excludePathPaterns("/user/login");
  }
}
```

### filter, interceptor 차이

| 구분       | Filter                                   | Interceptor                          |
| -------- | ---------------------------------------- | ------------------------------------ |
| 실행 위치    | 서블릿 컨테이너 레벨 (DispatcherServlet 이전)       | Spring MVC 레벨 (DispatcherServlet 이후) |
| 구현 인터페이스 | `javax.servlet.Filter`                   | `HandlerInterceptor`                 |
| 용도       | 요청/응답 전역 처리 (인코딩, 인증, 로깅 등)              | 컨트롤러 전후 로직 처리 (권한, 로깅 등)             |
| 등록 방법    | `@WebFilter`, `FilterRegistrationBean` 등 | `WebMvcConfigurer`에 등록               |


| 구분    | Spring Security 필터                  | Spring MVC 인터셉터                    |
| ----- | ----------------------------------- | ---------------------------------- |
| 구현 방식 | `javax.servlet.Filter` 인터페이스 구현     | `HandlerInterceptor` 인터페이스 구현      |
| 동작 위치 | 서블릿 필터 체인 내, `DispatcherServlet` 이전 | `DispatcherServlet` 이후, 컨트롤러 호출 전후 |
| 역할    | 인증, 인가, CSRF, 세션 관리 등 보안 전반 담당      | 요청 전후 로깅, 인증 상태 확인, 요청 가공 등        |
| 등록 위치 | 서블릿 컨테이너 필터 체인 또는 스프링 시큐리티 설정       | `WebMvcConfigurer`에서 등록            |
| 처리 범위 | 모든 HTTP 요청 (웹, API 등)               | 스프링 MVC가 처리하는 컨트롤러 요청에 한정          |
| 상태 관리 | 스레드 안전하게 설계해야 함                     | 요청 단위, 컨트롤러 라이프사이클에 맞춤             |
