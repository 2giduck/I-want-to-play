### Chapter 2. 네트워크
#### Section 2.1 네트워크의 기초 
- 네트워크: 컴퓨터 등의 장치들이 통신 기술을 이용하여 구축하는 연결망 
  - 노드와 링크가 서로 연결되어 있거나 연결되어 있으며 리소스를 공유하는 집단
###### 2.1.1 처리량과 지연 시간
- 좋은 네트워크
  - 많은 처리량을 처리할 수 있으며 
  - 지연 시간이 짧고 
  - 장애 빈도가 적으며 
  - 좋은 보안을 갖춤
- 처리량(bps: bits per second): 링크 내에서 성공적으로 전달된 데이터의 양
  - 얼만큼의 트래픽을 처리했는지
- 트래픽: 특정 시점에 링크 내에 '흐르는' 데이터의 양
- 대역폭: 주어진 시간 동안 네트워크 연결을 통해 흐를 수 있는 최대 비트 수
- 지연 시간(ms) : 요청이 처리되는 시간
  - 매체 타입, 패킷 크기, 라우터의 패킷 처리 시간에 영향 받음
###### 2.1.2 네트워크 토폴로지와 병목 현상
- 네트워크 토폴로지: 노드와 링크가 어떻게 배치되어 있는지에 대한 방식이자 연결 형태
  - 트리 토폴로지(계층형 토폴로지) : 트리 형태로 배치한 네트워크
    - 노드의 추가, 삭제가 쉬움. 
    - 특정 노드에 그래픽이 집중될 때 하위 노드에 영향을 끼칠 수 있음
  - 버스 토폴로지: 중앙 통신 회선 하나에 여러 개의 노드가 연결되어 공유하는 네트워크 구성
    - LAN에서 사용 
    - 설치 비용이 적고 신뢰성이 우수. 중앙 통신 회선에 노드를 추가하거나 삭제 쉬움
    - 스푸핑(LAN 상에서 송신부의 패킷을 관련 없는 다른 호스트에 가지 않도록 하는 스위칭 기능을 마비시키거나 속여서 특정 노드에 해당 패킷이 오도록 처리)이 가능
  - 스타 토폴로지: 중앙에 있는 노드에 모두 연결된 네트워크 구성
    - 노드를 추가하거나 에러를 탐지하기 쉬움, 충돌 발생 가능성이 적음
    - 중앙 노드에 장애가 발생하면 전체 네트워크를 사용할 수 없음. 설치 비용이 고가
  - 링형 토폴로지: 각각의 노드가 양 옆의 두 노드와 연결하여 전체적으로 고리처럼 통신을 하는 망 구성 방식
    - 노드 수가 증가되어도 네트워크상 손실이 거의 없음. 노드의 고장 발견을 쉽게 찾음.
    - 네트워크 구성 변경이 어려움. 회선에 장애가 발생하면 전체 네트워크에 크게 영향을 끼침
  - 메시 토폴로지: 망형 토폴로지. 그물망처럼 연결되어 있음 
    - 트래픽 분산 처리 가능. 한 단말 장치에 장애가 발생해도 사용 가능
    - 노드 추가 어려움. 비용이 고가.
- 병목 현상: 전체 시스템의 성능이나 용량이 하나의 구성 요소로 인해 제한을 받는 현상 

###### 2.1.3 네트워크 분류
- LAN(Local Area Network)
  - 사무실 규모
  - 근거리 통신망
  - 전송 속도가 빠르고 혼잡하지 않음
- MAN(Metropolitan Area Network)
  - 시 규모
  - 전송 속도는 평균. LAN보다 혼잡
- WAN(Wide Area Network)
  - 세계 규모 
  - 전송 속도는 낮음. MAN보다 혼잡

###### 2.1.4 네트워크 성능 분석 명령어
- 병목 현상 원인
  - 네트워크 대역폭
  - 네트워크 토폴로지
  - 서버 CPU, 메모리 사용량
  - 비효율적인 네트워크 구성 

- ping 
  - 네트워크 상태를 확인하려는 대상 노드를 향해 일정 크기의 패킷을 전송하는 명령어 
  - ping [IP 주소 또는 도메인 주소]
- netstat
  - 접속되어 있는 서비스들의 상태를 표시 
- nslookup
  - DNS와 관련된 내용 확인
  - 도메인에 매핑된 IP 확인
- tracert
  - 목적지 노드까지 네트워크 경로를 확인
  - 목적지 노드까지 구간들 중 어느 구간에서 응답 시간이 느려지는지 확인

###### 2.1.5 네트워크 프로토콜 표준화
- 네트워크 프로토콜: 다른 장치들끼리 데이터를 주고받기 위해 설정된 공통된 인터페이스
  - IEEE, IETF가 정함
  
#### Section 2.2 TCP/IP 4계층 모델

###### 2.2.1 계층 구조 
- TCP/IP 4계층
  - 애플리케이션 계층 
  - 전송 계층 
  - 인터넷 계층 
  - 링크 계층 
- OSI 7계층
  - 애플리케이션 계층
  - 프레젠테이션 계층
  - 세션 계층
  - 전송 계층
  - 네트워크 계층
  - 데이터 링크 계층
  - 물리 계층

- 특정 계층이 변경되었을 때 다른 계층이 영향받지 않도록 설계 

1. 애플리케이션 계층
  - (HTTP/SMTP/DNS/FTP/SSH)
  - 응용 프로그램이 사용되는 프로토콜 계층 
  - 웹 서비스, 이메일 등 서비스를 실질적으로 사람들에게 제공하는 층
2. 전송 계층
  - (TCP/UDP)
  - 송신자와 수신자를 연결하는 통신 서비스 제공 
  - 연결 지향 데이터 스트림 지원, 신뢰성, 흐름 제어 제공
  - 애플리케이션과 인터넷 계층 사이의 데이터가 전달될 때 중계 역할
3. 인터넷 계층
  - (IP/ARP/ICMP)
  - 
4. 링크 계층
  - (이더넷)


- TCP 3-way handshake
  1. SYN: 클라이언트 -> 서버 (ISN을 담아 SYN을 보냄)
  2. SYN + ACK: 서버 -> 클라이언트 (SYN 수신 + 서버의 ISN 송신) 
  3. ACK: 클라이언트 -> 서버 (ACK)
- TCP 4-way handshake

#### Section 2.3 네트워크 기기

#### Section 2.4 IP 주소

#### Section 2.5 HTTP
###### 2.5.1 HTTP/1.0
- HTTP/1.0: 기본적으로 한 연결당 하나의 요청을 처리하도록 설계됨
  - RTT 증가 
  - RTT: 패킷이 목적지에 도달하고 나서 다시 출발지로 돌아오기까지 걸리는 시간
  - 해결하기 위해 이미지 스플리팅, 코드 압축, 이미지 Base64 인코딩 

###### 2.5.2 HTTP/1.1
- 매번 TCP 연결을 하는 것이 아닌, 한 번 TCP 초기화를 한 이후에 keep-alive 옵션으로 여러 개의 파일 송수신
  - 다수의 리소스를 처리하려면 리소스 개수에 비례하여 대기 시간이 길엉짐
  - HOL Blocking(Head Of Line Blocking): 네트워크에서 같은 큐에 있는 패킷이 그 첫 번째 패킷에 의해 지연될 때 발생하는 성능 저하 현상

###### 2.5.3 HTTP/2
- 멀티 플렉싱, 헤더 압축, 서버 푸시, 요청의 우선순위 처리 지원
  - 멀티 플렉싱: 여러 개의 스트림을 사용하여 송수신
  
###### 2.5.4 HTTPS
- 애플리케이션 계층과 전송 계층 사이에 신뢰 계층인 SSL/TLS 계층을 넣은 신뢰할 수 있는 HTTP 요청

###### 2.5.5 HTTP/3
- QUIC 계층 위에서 돌아감
  - UDP 기반 
  - 멀티플렉싱을 가지고 있음
  - 초기 연결 설정 시 지연 시간 감소 