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
