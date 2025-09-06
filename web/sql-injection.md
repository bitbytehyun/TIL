# SQL Injection
공격자가 입력값에 SQL 구문을 삽입해서, 원래 의도와는 다른 쿼리를 실행시키는 공격 방법입니다.

1. 취약한 코드가 존재하고
    ```java
    String query = "SELECT * FROM users WHERE username = '" + userInput + "'";
    Statement stmt = conn.createStatement();
    ResultSet rs = stmt.executeQuery(query);
    ```
2. 공격자가 폼에 입력을 합니다. `' OR '1'='1`
3. 실제로 실행되는 쿼리는 `SELECT * FROM users WHERE username = '' OR '1'='1';`가 되어, `users` 테이블의 모든 데이터가 조회됩니다.

이를 방어하기 위해서는 다음 방법들이 있습니다. 
### 1. Prepared Statement (바인딩 변수)
SQL과 파라미터를 분리하여, DB 엔진이 미리 쿼리 구조를 확정합니다. 
```java
String query = "SELECT * FROM users WHERE username = ?";
PreparedStatement pstmt = conn.prepareStatement(query);
pstmt.setString(1, userInput);
ResultSet rs = pstmt.executeQuery();
```
입력 값은 데이터로만 처리하고, SQL 명령어로 해석되지 않습니다. 
### 2. ORM / 쿼리 빌더 활용
JPA, MyBatis, Exposed, Jooq 는 내부적으로 바인딩 처리합니다. 
### 3. 입력값 검증
허용된 값만 통과하도록 검증합니다.
### 4. 최소 권한 DB 계정 사용
어플리케이션의 계정은 `DROP`, `ALTER` 권한을 제거합니다. 
