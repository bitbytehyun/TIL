# BLOB, TEXT 란
대용량 데이터를 저장하는 타입이지만, 저장하는 데이터 성격에 차이가 있습니다. 

### BLOB (Binary Large Object)
* 이진 데이터를 저장합니다.
* 주로 이미지, 동영상, 음성 파일, 실행 파일 등 비문자 데이터 저장에 사용됩니다.
* MySQL 종류: `TINYBLOB`(최대 255B), `BLOB`, `MEDIUMBLOB`, `LONGBLOB`(최대 4G)

### TEXT
* 긴 텍스트같은 문자열 데이터를 저장합니다.
* 주로 기사 본문, 코멘트, 설명, 로그 메시지 등 문자 데이터 저장에 사용됩니다.
* Charset, Collation이 적용되어 정렬, 비교, LIKE 검색이 가능해집니다.
* MySQL 종류: `TINYTEXT`, `TEXT`, `MEDIUMTEXT`, `LONGTEXT`(최대 4G)

단, db에 대용량 BLOB, TEXT를 저장하면 성능이 저하되므로, 파일시스템이나 스토리지(S3 등)에 저장하고, DB에는 URL만 저장합니다. 
실무에서는 성능때문에 파일은 외부 스토리지, 텍스트는 전문 검색 엔진(full-text search, ElasticSearch 등)을 병행해서 사용합니다. 
