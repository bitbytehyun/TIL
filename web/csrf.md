# CSRF란
공격자가 사용자의 인증된 세션을 악용하여, 사용자가 원하지 않는 요청을 대신 보내도록 위조하는 공격입니다. 
1. 은행 사이트에 로그인하여, 세션 쿠키를 유지하고 있음
2. 공격자가 만든 악성 웹페이지에 접속
3. 페이지 내부에 악성 코드 존재 `<img src="https://bank.com/transfer?to=attacker&amount=10000">`
4. 브라우저는 자동으로 쿠키를 붙여 요청을 보냄
5. 은행 서버는 요청을 받아서 처리하여 돈을 이체시킴

이같은 방법을 사용하는 공격 방식이고, 이를 방어하기 위해서는 다음 방법을 사용할 수 있습니다. 
### 1. CSRF 토큰 방식
* 서버가 폼/요청마다 랜덤 토큰을 발급하고,
* 클라이언트는 요청 시 토큰을 같이 제출합니다.
* 공격자는 토큰을 알 수 없어서 위조가 불가능해집니다.
```html
<form method="POST" action="/transfer">
  <input type="hidden" name="csrfToken" value="abc123xyz">
</form>
```
### 2. SameSite 쿠키 옵션
* 쿠키에 SameSite 속성을 추가하여, 다른 사이트에서 자동으로 전송되지 않도록 합니다.
* `Strict`: 완전 차단
* `Lax`: 안전한 요청 (GET, a링크만 허용)
* `None`: 크로스 사이트 허용
```html
Set-Cookie: JSESSIONID=abcd; SameSite=Lax; Secure; HttpOnly
```
### 3. Referer, Origin 체크
요청 헤더의 출처를 검사해서 허용된 도메인에서만 처리합니다.
### 4. 추가 인증 절차
중요한 요청(비밀번호 변경, 송금 등)에는 재로그인/OTP 등을 요구합니다.
