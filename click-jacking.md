# Clickjacking이란
사용자가 보이지 않은 UI를 클릭하도록 속여서, 의도하지 않은 동작을 수행하는 공격입니다. 
1. 사용자가 은행 웹사이트에 로그인하여 세션 유지 중
2. 공격자가 만든 웹사이트에 접속
3. 페이지 안에 투명한 iframe이 숨어있음
    ```html
    <iframe src="https://bank.com/transfer?to=attacker&amount=10000"
        style="opacity:0; position:absolute; top:0; left:0; width:100%; height:100%;">
    </iframe>
    ```
4. 사용자가 보이지 않은 버튼을 누르면, 실제로는 은행 송금 버튼을 누르는 것


방어 방법으로는 다음 방법들이 있습니다. 
### 1. X-Frame-Options 헤더
HTTP 응답 헤더로 iframe 삽입을 차단합니다. 
```http
X-Frame-Options: DENY          # 어떤 사이트에서도 iframe 불가
X-Frame-Options: SAMEORIGIN    # 같은 도메인만 허용
```

### 2. CSP frame-ancestors 
더 세밀하게 iframe 허용 도메인을 제어합니다.
```http
Content-Security-Policy: frame-ancestors 'self' https://trusted.com;
```
### 3. UI 보안 기법
* 중요한 버튼은 더블 클릭/추가 인증을 요구합니다.
* 클릭 이벤트 발생 시, 사용자 동작을 검증합니다.
