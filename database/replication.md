# 리플리케이션이란
## 실제 개념
하나의 DB를 다른 서버에 복제해서 동일하게 유지하는 기술입니다. 
주로 장애시 대체 서버로 서비스를 지속할 수 있는 **고가용성**, 일긱 전용 요청을 Replica 서버로 분산하여 **부하 분산**, 장애 시 데이터 손실 최소화를 위해 활용됩니다. 


**Master + Slave (= Primary + Replica)**
* Master: 쓰기 전용
* Slave: 읽기 전용으로, Master의 변경사항을 복제
* 쓰기 성능은 Master에 종속되고, 읽기 성능은 Replica 여러 대로 확장이 가능합니다.
* MySQL의 기본 복제 구조, PostgreSQL Streaming Replication이 이에 해당됩니다.

**Master + Master (= Multi Primary)**
* 여러 Master가 동시에 읽기와 쓰기를 처리합니다.
* 단, Master간 데이터 동기화가 필요하며, 충돌 가능성이 있어서 해결할 수 있는 로직이 필요합니다. (어떤 충돌??)
* 쓰기 성능 확장이 가능하나, 충돌 처리 비용과 실시간 동기화 부하가 큽니다.
* MysSQL Group Replication이 이에 해당됩니다.

**동기식**
* Master가 Replica에 성공적으로 기록될 떄까지 Master는 commit 되지 않습니다.
* 일관성이 강하지만, 네트워크 지연으로 성능이 저하될 수 있습니다.

**비동기식**
* Master는 바로 commit되고, 이후에 Replica로 전달됩니다.
* 속도가 빠르지만, 장애 시 Replica 데이터는 유실될 수 있습니다.

**반동기식**
* Master는 적어도 하나의 Replica에 전달된 것을 확인하고 commit 합니다.

### 활용
* **읽기 부하 분산**: 대규모 서비스에서 select 요청을 Replica 서버로 분산시켜, 읽기 부하를 분산시킬 수 있습니다.
* **고가용성**: 운영 Master 부하를 줄이기 위해 백업 또는 분석용 쿼리는 Replica에서 실행할 수 있습니다.
* **백업 / 분석 서버 분리**: Master 장애 시, Replica를 승격시켜 서비스를 지속시킬 수 있는 Failover 시스템을 구축할 수 있습니다.
