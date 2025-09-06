# RESTful이란
REST(Representational State Transfer)은 웹 아키텍처 스타일입니다. 
핵심 아이디어는 다음과 같습니다.
* 웹의 자원을 URI로 표현
* 자원을 다루는 행위는 HTTP 매서드로 구분
* 서버는 무상태로 동작

이런 REST 원칙을 준수하여 설계된 API를 RESTful API라고 칭합니다. 
RESTful API의 특징으로는 다음이 있습니다. 
1. 자원 기반: URI에 명사를 사용합니다. `/users`, /orders/123`
2. 행위는 HTTP 매서드로 표현:
    * `GET /users`: 사용자 목록 조회
    * `POST /users`: 사용자 생성
    * `PUT /users/123`: 사용자 수정
    * `DELETE /users/123`: 사용자 삭제
1. Stateless: 요청 간 상태를 저장하지 않고, 클라이언트가 필요한 모든 정보를 요청에 담는다.
2. 응답 표준화: HTTP 상태 코드를 활용하여 200 OK, 201 Created, 404 Not Found, 500 Internal Server Error 등이 있습니다.
3. 계층적 구조: API 게이트웨이, 캐싱 서버, 인증 서버 등을 계층적으로 두더라도 client-server 통신 방식은 변하지 않습니다.
4. HATEOAS: 응답에 다음 행동으로 가능한 링크를 `_links` 필드로 제공합니다.


### 장단점
**장점**
* 표준적이고 직관적이므로, client-server 협업에 용이합니다.
* HTTP 인프라 (캐싱, 로드밸런싱 등)을 활용 가능합니다.

**단점**
* 너무 엄격히 지키면 비효율적일 수 있음

  
