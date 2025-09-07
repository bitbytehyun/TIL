# CORS란
CORS란 교차 출처 리소스 공유로, 브라우저에서 다른 출처의 리소스를 요청할 때, 보안상 허용할지 말지 결정하는 정책 및 규약입니다. 


브라우저에는 동일 출처 정책 (Same-Origin Policy)가 있습니다. 출처(origin)이란, `프로토콜 + 도메인 + 포트`입니다. 
출처가 다르면, 기본적으로 AJAX, Fetch API 요청을 차단합니다. 
**예시**
* 프론트앤드: `https://stu.com`
* 백앤드: `https://stu.api.com`
이처럼 출처가 달라서 브라우저가 이를 그냥 막습니다. 이를 풀어주는 것이 CORS 입니다.

### CORS 동작 원리
1. 단순 요청
    * GET, POST, HEAD 같은 단순 요청은 바로 전송합니다.
    * 서버가 응답에 `Access-Control-Allow-Origin` 헤더를 달아주면 허용됩니다.
1. 사전 요청
    * `PUT`, `DELETE` 등 안전하지 않은 요청의 경우, 브라우저가 먼저 `OPTIONS` 요청을 보내서 허용 여부를 확인합니다.
    ```http
    OPTIONS /api/data
    Origin: https://studymoa.com
    Access-Control-Request-Method: DELETE
    ```
    * 서버가 응답합니다
    ```http
    Access-Control-Allow-Origin: https://studymoa.com
    Access-Control-Allow-Methods: GET, POST, DELETE
    Access-Control-Allow-Headers: Content-Type, Authorization
    ```
    * 이 때, 브라우저가 허용되면 실제 요청을 실행합니다. 
