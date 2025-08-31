# 옵티마이저 힌트란
힌트란, 옵티마이저에게 실행 계획을 강제로 유도하는 지시문입니다. 
통계가 부정확하여 비용 기반 옵티마이저에서 오류를 낼 때, 개발자는 힌트를 줘서 강제로 쿼리 작동 방식을 유도할 수 있습니다. 
또한, 긴급 성능 이슈로 즉시 성능 개선이 필요할 때 사용할 수 있습니다. 
단, 힌트는 DB와 버전에 의존적이므로, 이식성이 떨어지고, 인덱스 구조 변경 시 독이 될 수 있습니다. 
따라서, 임시 조치로 쓰되, 근본적으로 인덱스 설계, 통계 갱신, SQL 재작성(튜닝)으로 해결하는 것이 좋습니다. 

### Oracle
인덱스 뿐만 아니라, 조인 순서, 조인 방식, 병렬화, 실행 전략 등 전체 최적화를 제어할 수 있는 옵티마이저 힌트를 제공합니다. 
```sql
SELECT /*+ FULL(e) */ *
FROM employees e
WHERE department_id = 10;
```
* `FULL (table)`: 풀 스캔 강제
* `INDEX(table index_name)`: 특정 인덱스 사용
* `LEADING(table)`: 조인 순서 강제
* `USE_NL(table)`: Nested Loop Join 강제
* `USE_HASH(table)`: Hash Join 강제
* `PARALLEL(table, degree)`: 병렬 실행



### MySQL
MySQL은 주로 옵티마이저 힌트가 아니라, 인덱스 힌트로 제공됩니다. 
인덱스 힌트로 인덱스 사용 여부만 제어할 수 있습니다. 
```
SELECT * 
FROM orders FORCE INDEX (idx_customer_id)
WHERE customer_id = 123;
```
* `USE INDEX (idx_name)`: 옵티마이저가 해당 인덱스 사용을 고려합니다.
* `FORCE INDEX (idx_name)`: 해당 인덱스를 반드시 사용합니다.
* `IGNORE INDEX (idx_name)`: 특정 인덱스를 무시합니다.


