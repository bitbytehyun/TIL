# 파티셔닝이란
## 실제 개념
파티셔닝이란, 큰 데이터이나 인덱스를 여러개의 파티션 단위로 나눠서 저장하는 기술입니다. 
사용자 입장에서는 하나의 테이블이지만, 내부적으로는 여러개의 물리적 단위에 분산 저장됩니다. 

**Range Partitioning**
* 특정 범위 기주능로 데이터를 분할합니다.
```sql
CREATE TABLE orders (
  order_id INT,
  order_date DATE
)
PARTITION BY RANGE(YEAR(order_date)) (
  PARTITION p2023 VALUES LESS THAN (2024),
  PARTITION p2024 VALUES LESS THAN (2025)
);
```

**List Partitioning**
* 특정 값 목록에 따라 분할합니다.
```sql
PARTITION BY LIST (region) (
  PARTITION asia VALUES IN ('Korea', 'Japan', 'China'),
  PARTITION europe VALUES IN ('France', 'Germany')
);
```

**Hash Partitioning**
* 해시 함수를 적용해서 데이터를 분할합니다. 특정 값이 몰리지 않는 균등 분산에 유리합니다.

**Composite Partitioning**
* 두가지 파티셔닝 방식을 결합합니다.
* 예: Range + Hash는 먼저 기간별로 나눈 뒤, 각 기간의 파티션을 해시로 분산시킵니다.

### 장점
* **성능 향상**: 특정 파티션만 읽으면 되므로, 쿼리 범위를 제한하여 I/O가 감소될 수 있습니다.
* **관리 용이**: 특정 파티션만 백업과 복구가 가능하고, 오래된 파티션을 통째로 DROP이 가능합니다.
* **가용성**: 일부 파티션이 손상되더라도, 나머지 데이터는 정상적으로 사용이 가능합니다.
### 단점
* 특정 파티션에 몰리면, 쓰기 성능이 저하될 수 있습니다.
* 설계와 운영이 복잡해질 수 있습니다.
* 글로벌 인덱스 (모든 파티션에 적용되는 인덱스) 가 dbms에 다라 제공을 하지 않을 수 있습니다.

### 언제 고려되는지
한 DB 인스턴스 내에서 테이블이 너무 커질 때 사용합니다. 
* **데이터 용량**: 매출 로그, 접속 이력과 같이 데이터 용량이 수억건이면, Full Scan 시 성능 저하의 위험이 있을 때
* **쿼리 패턴**: 범위 검색(`WHERE date BETWEEN..`)이 잦거나, 오래된 데이터는 거의 조회하지 않고 최신 데이터만 조회할 때
* **관리 편의성**: 오래된 데이터를 아카이빙할 때 파티션 단위로 DROP이 가능하고, 특정 기간의 데이터만 백업/복구하고 싶을 때

## 내가 이애한 바
* aws athena에서 기본 값으로 사용했던 것 같음 . 특히 통계 사용할 때

