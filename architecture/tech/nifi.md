# Nifi란
## 실제 개념
Apache Nifi는 데이터 흐름을 시각적으로 설계하고 실행하는 ETL 데이터 파이프라인 도구입니다. 
데이터 수집 (Ingest) -> 변환 (Transform) -> 전송 (Load) 하는 과정을 GUI로 쉽게 만들고 관리할 수 있습니다.
데이터 플로우 기반 아키텍처로, Producer, Consumer 코드를 직접 짜는 게 아니라 Processor 블록을 이어 붙여서 동작을 정의할 수 있습니다.
### 특징
1. 시각적 플로우로 디자인할 수 있습니다.
    * 브라우저 UI에서 드래그 앤 드롭으로 각 단계별로 프로세스를 연결하여 데이터 flow 구성할 수 있습니다.
    * 예: `[GetFile] → [ConvertRecord(JSON→CSV)] → [PutKafka]`
1. 데이터 처리를 자동화할 수 있습니다.
    * 파일, DB, 메시지 큐(Kafka, Kinesis 등)과 연동이 가능합니다.
    * 데이터 포맷(json, csv 등)을 변환할 수 있고, 필터링, 암호화 등 가능합니다.
1. 데이터 보장이 가능합니다.
    * 순서 보장: 프로세서 간 내부 큐에서 순서 유지가 가능합니다.
    * 데이터 보존: 전송 실패 시, 재시도/재전송이 가능합니다.
    * 백프레셔: 다운스트림이 느리면 데이터 수집 속도를 자동으로 조절합니다. ???? 
1. 연결이 유연합니다.
    * Kafka, Kinesis, S3, RDMBS 모두 연동 가능합니다.
    * 실시간, 배치 둘 다 가능합니다.
  
### 대체 가능 제품
* GUI 기반 ETL이 필요하다 → NiFi, StreamSets, Talend, Hop
* 실시간 데이터 처리가 핵심이다 → Flink, Kafka Streams, Spark Streaming
* 배치 파이프라인 관리가 목적이다 → Airflow, Prefect
