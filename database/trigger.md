# 트리거란
## 실제 개념
트러기러나, 특정 이벤트(insert, update, delete)가 발생했을 때, 자동으로 실행되는 SQL 블록입니다. 
DB 자동화 규칙으로, 사용자가 호출하지 않아도 DB가 자동으로 수행합니다. 

예를 들어, 다음 트리거를 저장하면, `users` 테이블에서 `UPDATE` 발생 시, 자동으로 `updated_at`을 현재 시각으로 갱신합니다.
```sql
CREATE TRIGGER update_timestamp
BEFORE UPDATE ON users
FOR EACH ROW
SET NEW.updated_at = NOW();
```
주로 다음과 같은 상황에서 쓰일 수 있습니다.  
* 자동 로그 기록: 데이블 데이터 변경 시, 로그 테이블에 자동 기록
* 데이터 무결성 유지: 음수 금액 입력 시 에러 발생 등 잘못된 데이터의 삽입과 수정을 방지
* 자동 갱신: UPDATE 발생 시 updated_at을 자동 수정하거나, 재고 수량 변경 시 관련 테이블을 동기화할 수 있습니다. 


### 종류
* 시점 기준으로는 `BEFOER TRIGGER`, `AFTER TRIGGER`로, 이벤트 실행 직전과 직후에 실행됩니다.
* 이벤트 기준으로는 `INSERT TRIGGER`, `UPDATE TRIGGER`, `DELETE TRIGGER`로 행이 삽입/수정/삭제될 때 실행됩니다. 

### 장단점
* DB 레벨에서 데이터 무결성을 보장할 수 있습니다.
* DB 자동화하여, 개발자가 놓칠 수 있는 로직을 DB가 보완할 수 있습니다.
* 로직이 DB 안에 숨겨져 있습니다. 복잡한 비즈니스 로직은 어플리케이션 레벨에서 처리하는 것이 더 적합합니다. 
* 과도하게 사용하면 특히 대량 UPDATE/DELETE 시, 성능이 저하될 수 있습니다.
