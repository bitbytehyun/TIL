# 쿼리 익스플레인 보는 법
SQL 성능 튜닝에서 EXPLAIN 실행계획을 읽는 법은 중요합니다. 
EXPLAIN이란, DB 옵티마이저가 선택한 실행 계획을 보여줍니다. 실제 쿼리는 동작하지 않지만, 어떻게 실행할 건지 동작을 확인할 수 있습니다. 


```sql
EXPLAIN
SELECT o.order_id, c.name
FROM Orders o
JOIN Customers c ON o.customer_id = c.id
WHERE c.region = 'Korea';
```
이와 같은 쿼리를 실행하면, 다음과 같이 출력됩니다. 
| id | select\_type | table | type | possible\_keys | key          | rows | Extra                 |
| -- | ------------ | ----- | ---- | -------------- | ------------ | ---- | --------------------- |
| 1  | SIMPLE       | c     | ref  | idx\_region    | idx\_region  | 100  | Using where           |
| 1  | SIMPLE       | o     | ref  | fk\_customer   | fk\_customer | 1000 | Using index condition |

여기서, type은 조인 및 탐색 방법을 뜻하는 칼럼으로, 성능을 크게 좌우하는 항목입니다. 
`ALL`은 풀테이블 스캔, `ref/range`는 인덱스를 사용하는 방식으로, `system > const > eq_ref > ref > range > index > ALL` 순서로 느려집니다. 

id는 높을수록 먼저 실행되는 순서를, key 칼럼은 실제로 선택된 인덱스를 뜻합니다.
