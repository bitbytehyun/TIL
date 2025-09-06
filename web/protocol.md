# HTTP 이외의 프로토콜
## 실제 개념
주 계층별로 사용되는 프로토콜을 정리하여 나열합니다. 

### 1. 응용 계층
사용자와 가장 가까운 계층으로, 우리가 자주 접하는 프로토콜들입니다. 
#### 파일/메일
* FTP (File Transfer Protocol): 파일 전송용 (포트 20, 21), 지금은 보안 문제로 SFTP, FTPS를 많이 사용합니다.
* SMTP (Simple Mail Transfer Protocol): 메일 발송용 (포트 25, 587, 465)
* IMAP / POP3: 메일 수신용 (IMAP는 서버 동기화, POP3는 로컬 다운로드)
#### 네트워크 인프라
* DNS (Domain Name System): 도메인을 IP 주소로 변환합니다.
* DHCP (Dynamic Host Configuration Protocol): IP 주소를 자동으로 할당합니다.
* NTP (Network Time Protocol): 서버/클라이언트 시간을 동기화합니다.
#### 원격 접속
* SSH (Secure Shell): 원격 접속하여 명령어를 실행합니다. (포트 22)
* TELNET: 예전의 원격 접속 프로토콜입니다. 보안이 없어 거의 사용하지 않습니다.


### 2. 전송 계층
#### 전송 방식
* TCP (Transmission Control Protocol): 연결 지향적, 신뢰성 있는 전송으로 웹, 메일, 파일 전송 등에 사용됩니다.
* UDP (User Datagram Protocol): 비연결성으로 빠르지만 신뢰성이 없어 게임, 스트리밍, VolP에 사용됩니다.

### 3. 인터넷 계층
#### 네트워크 기반
* IP (Internet Protocol): 주소 지정하여 패킷을 전달합니다. 
* ICMP (Internet Control Message Protocol): 네트워크를 진단합니다. (예: `ping` 명령어)
* ARP (Adress Resolution Protocol): IP 주소를 MAC 주소로 매핑합니다.

### 4. 링크 계층
* Ethernet: 유선 LAN의 표준 프로토콜입니다.
* PPP (Point-to-Point Protocol): 전화선/모뎀 기반 연결에 사용되던 프로토콜입니다.



### 중요한 주요 프로토콜 중심
#### HTTP/HTTPS
* 웹서비스의 기본 프로토콜
* REST API, GraphQL, gRPC 모두 HTTP 위에서 동작
* HTTPS = HTTP + TLS 보안으로 오늘날 필수
* 알아야 할 포인트
    * 요청/응답 구조 (매서드, 상태 코드, 헤더, 바디)
    * 버전별 차이
    * 쿠키, 세션, JWT 인증 동작 방식
#### TCP / UDP
* 전송 계층 기본으로, 모든 앱 프로토콜의 기반
* TCP: 신뢰성, 순서 보장 (HTTP, DB 연결, SSH 등)
* UDP: 빠르지만 신뢰성 없음 (게임, 스트리밍, DNS 등)
* 알아야 할 포인트
    * TCP 3-way handshake, 4-way 종료 과정
    * 포트 개념: 80, 443, 3306, 22 등
    * NAT, 방화벽과의 연관성
#### SSH
* 리눅스 서버 원격 접속 (포트 22)
* 배포, 서버 관리에 필수
* 알아야 할 포인트
    * 공개키/비밀키 인증 방식
    * SSH 터널링 (포트 포워딩)
#### SQL 프로토콜 (DB 통신)
* MySQL, PostgreSQL 같은 DB는 자체 프로토콜을 TCP 위에서 사용
* 예: MySQL (포트 3306), Postgre (포트: 5432)
* 알아야할 포인트
    * 커넥션 풀 관리하여 성능/안정성
    * DB 인증 및 암호화 (TLS)
