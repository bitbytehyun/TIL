# 복합 인덱스란
둘 이상의 칼럼을 묶어서 만든 인덱스입니다. 
여러 조건이 함께 사용되는 where 절에서 효율적으로 검색이 가능합니다. 
```sql
CREATE INDEX idx_orders_customer_date
ON Orders (customer_id, order_date);
```
해당 인덱스는 B+Tree 구조에서 (customer_id + order_date) 조합으로 정렬되어 저장됩니다. 
왼쪽부터 차례대로 탐색할 수 있으므로, 인덱스 정의 순서가 매우 중요합니다. 
예를 들어, `where customer_id = 10`은 인덱스를 탈 수 있으나, `where order_date >= '2025-01-01'은 인덱스를 타지 못합니다. 

### 장점
* 다중 where 조건의 검색 성능이 향상됩니다.
* covering index 효과로, select 칼럼이 모두 인덱스에 포함되면, 디스크 접근이 불필요합니다.

### 단점
* 인덱스의 크기가 중가하여, 쓰기 성능이 저하될 수 있습니다.
* 선행 칼럼이 조건절에 없으면 인덱스의 효율이 떨어집니다. 
