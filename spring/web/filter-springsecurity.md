# spring security의 기본적인 filter
## 실제 개념
Spring Security는 웹 앱에서 인증, 인가 기능을 제공하기 위해 여러개의 필터를 체인으로 만들어서 동작합니다.
* 인증: 사용자가 누구인지 확인 (예: 아이디와 비밀번호)
* 인가: 인증된 사용자가 특정 자원에 접근할 권한 판단
이 필터들은 HTTP 요청을 DispatcherServlet에 도달하기 전에 가로채서 보안 작업을 수행합니다. 

### `SecurityContextPersistenceFilter`
* 인증 상태를 유지하는 핵심 필터입니다. 
* HTTP 요청이 들어오면 인증 정보와 권한 정보가 담긴 `SecurityContext`를 세션이나 다른 저장소에서 불러오고, 이를 응답 시점에 다시 저장합니다. 
* 동작 시점 : 필터 체인의 가장 앞부분에서 인증 정보를 복원합니다.

### `UsernamePasswordAuthenticationFilter`
* 폼 로그인 시, 사용자의 이름과 비밀번호를 받아서 인증을 처리합니다.
* 입력받은 아이디/비밀번호를 `AuthenticationManager`에게 전달해 인증을 위임합니다.
* 동작 시점 : 기본적으로 `POST /login`의 로그인 요청에 매핑되어 동작합니다.

### `BasicAuthenticationFilter`
* HTTP 요청에 포함된 HTTP Bearer 인증 헤더를 파싱해서 인증을 처리합니다.
* Bearer 토큰과는 다르며, HTTP Basic 인증 방식에 사용됩니다. (???)
* 동작 시점 : HTTP Basic 인증이 필요한 요청에서 동작합니다.

### `BearerTokenAuthenticationFilter`
* HTTP 요청에 포함된 Bearer 토큰(예: JWT)을 파싱해 인증을 수행합니다.
* 주로 OAuth2 리소스 서버 설정 시 사용됩니다.

### `ExceptionTranslationFilter`
* 인증 또는 인가 실패 시 발생하는 예외를 처리합니다.
* 인증 실패(401)이나 인가 실패(403) 발생 시 적절한 응답을 반환하거나 로그인 페이지로 리다이렉트 합니다.
* 동작 시점 : 인증 관련 필터 뒤에서 예외를 잡아 처리합니다.

### `FilterSecurityInterceptor`
* 최종 인가(권한) 검색을 수행합니다.
* 현재 인증된 사용자가 요청 자원에 대한 권한이 있는지 판단합니다.
* 동작 시점 : 가장 마지막 필터로, 실제 리소스(예: DispatcherSErvlet)로 요청이 전달되기 전에 권한을 확인합니다.

### `LogoutFilter`
* 로그아웃 요청이 오면 세션 무효화, 쿠키 삭제 등을 처리합니다.

필터 체인은 스프링 시큐리티가 기본적으로 순서대로 구성합니다. 일반 사용자는 이 필터 체인을 그대로 사용합니다. 
필요하다면 `SecurityFilterChain` 빈을 직접 정의하거나, `WebSecurityConfigurerAdapter` 등을 통해 기존 설정으로 필터 추가, 제거, 순서 변경등을 할 수 있습니다.
