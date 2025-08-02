# spring mvc란 (8"00까지)
## 실제 개념
스프링 MVC란 스프링 프레임워크에서 제공하는 자바 Servlet 기반의 웹 앱 아키텍처입니다. 
HTTP 요청부터 비즈니스 로직 처리, 클라이언트에게 응답을 반환하기까지의 과정을 MVC 패턴으로 구현했습니다.
MVC 패턴은 요청 처리의 핵심을 역할별로 분리합니다.
1. Model: 어플리케이션의 핵심 데이터와 그 상태 (예: 회원 정보, 주문 내역 등)
2. View: 사용자에게 보여지는 화면
3. Controller: 흐름 중재자로, 클라이언트에게 요청을 받아서 서비스에 작업을 위임하고 결과를 다시 뷰로 전달합니다. 이 때, Model에 결과 데이터를 담아서 View로 전달합니다. 

스프링 MVC는 웹 요청을 프론트 컨트롤러 역할의 `DispatcherServlet`이 처리합니다. 
이 서블릿은 톰켓같은 서블릿 컨테이너에 등록되며, Spring의 WebApplicationContext(컨테이너)를 통해 싱글톤으로 관리됩니다.
요청마다 새로 생성되지 않고, 재사용됩니다. 
### 전체 흐름
1. 클라이언트가 HTTP 요청 전송
2. 서블릿 컨테이너(예: 톰캣)이 요청 수신
3. 서블릿 컨테이너에 등록된 `DispatcherServlet`의 `service()` 매서드 호출
4. `DispatcherServlet`은 URI에 맞는 컨트롤러를 HandlerMapping을 통해 탐색
5. `HandlerAdapter`로 결정된 컨트롤러를 호출
6. 컨트롤러는 서비스 계층을 호출해서 비즈니스 로직을 처리
7. 컨트롤러는 결과를 `Model`에 담고, 논리적인 뷰 이름을 반환
8. `DispatcherServlet`은 `ViewResolver`로 뷰 이름을 실제 뷰(예: JSP, Thymleaf)로 변환
9. `View`객체가 HTML을 랜더링하거나 JSON을 만들어 클라이언트에게 응답 전송
* REST API의 경우, HTML이 아니라 JSON을 주고 받으므로 `@RestController`, `@ResponseBody`가 붙은 매서드의 반환 객체는 `ViewResolver`가 아닌 `HttpMessageConvertr`가 적용되어, JSON이나 XML로 자동 변환됩니다.

따라서, Spring MVC는 서블릿의 복잡한 API를 추상화해서 다음과 같은 구조적 장점을 제공합니다.
1. 톰캣은 요청을 받아서 `DispatcherServlet`에 전달
2. `DispatcherServlet`은 전체 요청의 흐름을 조율
3. Controller는 핵심 비즈니스 처리를 담당
4. View, MessageConverter은 그 결과를 사용자에게 전달
   
    
