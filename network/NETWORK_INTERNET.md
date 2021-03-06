# 1. 네트워크와 인터넷
## 1-1) 기본 용어
### 네트워크
- 종단 시스템 : PC 나 스마트폰처럼 네트워크를 송수신하는 주체
- 프로토콜 : 두 가지 다른 시스템을 연결하기 위한 규약
- 통신하기 위해서 물리적으로 선으로 연결되어 있거나 무선으로 연결된다

### 인터넷
- 네트워크 간의 연결. 네트워크를 서로 다 연결하면 인터넷이 되는 것
- 전세계 네트워크와 연결된 상태
- 다양한 프로토콜을 지니고 있고, 그중 브라우저에서 사용하는 게 http

### OSI 7계층(Layer)
- 네트우크 구성 요소를 7개의 계층으로 역할을 나눈 표준 모델
- 각 계층별 역할로 통신 규격을 만족
- 일부 하위계층은 하드웨어에서 구현되고 상위계층은 소프트웨어로 구현
- OSI 7 계층 전에는 리눅스, 유닉스가 존재. (TCP/IP)

### TCP/IP(Transmission Control Protocol / Internet Protocol)
- OSI 7 계층이 나오기 전에 널리 사용되던 사실상 표준 역할
- OSI 7계층에 포함됨

### IP 주소(Address)
- 통신 자료를 최종적으로 전달하기 위해 필요한 송/수신의 위치 정보
  - 우편물을 보내듯이 보내는 곳과 받는 곳
- 보통 IPv4 의 주소를 사용하지만 주소가 부족해져서 IPv6 이 개발됨

### 패킷 교환(Packet Switching)
- 패킷은 인터넷을 위해서 서로 주고 받을 작은 단위를 의미
- 종단간에  전송되는 데이터를 패킷이라는 단위로 전달하는 것
- 패킷을 잘 주고 받기 위해서 중간에 교환하는 역할이 필요
- 패킷은 네트워크를 통해 일정한 순서 없이 보내지고, 같은 경로를 사용하지 않는다

## 1-2) 통신을 위해 필요한 기본 동작 방식
### 요청(Request)
- 전송하는 종단 장치에서 상대방에 서비스를 요청하는 것
### 인지(Indicate)
- 수신하는 장치에서 작업 요청을 확인하는 것
### 응답(Response)
- 수신하는 장치에서 요청받은 작업에 적절히 응답해주는 것

### 확인(Confirm)
- 전송 측에서 응답 데이터를 최종적으로 확인하는 것

## 1-3) 네트워크 유형
### LAN (Local Area Network)
- 일정 그룹의 네트워크 (집, 사무실, 학교 등)
- 소규모로 묶이고 사설망 등을 이용해서 구축한다
### WAN (Wide Area Network)
- 원거리 통신망으로 넓은 범위를 연결한다 (국가, 대륙 등)
- WAN 이 통합되면 인터넷이 되는 것

## 1-4) 네트워크 토폴리지(Topology)
- 네트워크 구성 형태를 의미

### Ring Topology
- 기기끼리 서로 연결하는 형태
- 장점은 기기 간의 연결이라 회선 설치가 수월
- 다만, 장비 중 하나가 불량이 생기면 특정 장비와 통신하기 어려울 수 있음

### Bus Topology
- 하나의 메인 회선을 설치하고 메인 회선에 모든 기기를 달아 놓는 형태

### Star Topology
- 하나의 허브가 있고 그 허브의 포트에 기기를 연결하는 것
- 허브의 위치에 따라 회선을 전부 끌어오기에 설치가 어려울 수 있음
- 보통 집에서 많이 사용하는 형태

### Mesh Topology
- 하나의 장비가 여러 회선을 가지고 다수의 장비에 연결할 수 있다
- 가장 빠르지만 설치 비용이 커진다

# 2. 네트워크 계층별 역할
## 2-1) OSI 7 Layer VS TCP/IP
- 네트워크의 표준이 만들어지기 전, 사실상 표준 TCP/IP 
  - TCP/IP 는 리눅스 운영체제에서 출발. 
  - 자주 사용하던 프로토콜 스택을 기반으로 OSI 7 계층을 만들게 됨

### Physical
- Physical
  - 가장 하위. 
  - 2진 비트의 나열로 Signal 처리
- Datalink
  - 두 Node 의 흐름을 제어

### Network
- Network
  - 해당 주소 체계에 따라 경로를 설정
  - IP Address 를 갖고 있기 때문에 이 계층에는 IPv4 와 IPv6 가 사용됨

### Transport
- Transport
  - 양쪽 끝 간의 신뢰성을 보장
  - TCP/UDP 등의 프로토콜을 추가로 갖게 됨

### Application
- Session
  - 프로그래머의 역할이 필요
  - 프로그램 간의 논리적 접속
- Presentation
  - 프로그래머의 역할이 필요
  - 문법, 구문, 체계 등을 확립
- Application
  - Email, Web 브라우저, ping, FTP 등

## 2-2) 종단 간의 연결 (간단 비유)
- (1) Application : 데이터를 받음
- (2) Transport : 데이터를 포장
- (3) Network : 포장된 데이터에 주소를 덧붙이기
- (4) Datalink : 하드웨어 끼리의 주소를 덧붙이기
- (5) Physical : 1과 0의 형태의 이진 값으로 데이터를 전송 시작
- (6) 경로를 제대로 파악하기 위해 Physical -> Datalink -> Network 까지 풀어 헤쳤다가 다음 경로로 보냄
- (7) 원하는 종단에 도착할 때까지 (6) 의 과정을 반복적으로 거침
- (8) 원하는 종단에 도착하면 Application 까지 풀어서 Data 를 전송 받음


# 3. Physical (물리 계층)
## 3-1) 개요
- 물리적 매체를 통해 Bit Stream(비트를 쭉 보내는 과정을 의미)에 요구되는 기능을 담당
- 기계적, 전기적, 전송매체를 이용해 통신하는 것. 물리적 회선이나 안테나 등이 필요 = Transmission Medium
- 물리적인 장치와 인터페이스가 전송을 위해 필요한 기능과 처리 절차를 규정하는 것

## 3-2) 물리 층의 주요 기능
- 인터페이스와 매체의 물리적인 특성
  - 장치와 전송매체 간의 인터페이스 특성을 규정
- 비트의 표현
  - 비트를 전송하기 위해 전기적 혹은 광학적 신호로 부호화
  - 이진표기법으로 채워 넣기 위한 것
- 데이터 속도
  - 신호가 유지되는 비트의 주기를 규정
  - 무한정 빠른 속도로 보낼 수 없고 Transmission medium 이 견뎌낼 수 있을 정도로만 보내는 것
- 비트의 동기화
  - 송신자와 수신자가 같은 클록을 사용
  - 클록 사이클에 맞춰서 비트 데이터를 받아들일 수 있음

## 3-3) 물리계층을 위한 프로토콜
### MAC(Medium Access Control) 필요
- 두 개의 전달하고자 하는 양 단에서 이진 비트를 보낼 때 상대방도 비트를 보내면 Conflict 발생
- 회선을 경쟁적으로 쓴다는 의미에서 자유 경쟁 알고리즘 활용 (선착순)
  - Aloha 
  - Slotted Aloha
  - CSMA (Carrier Sense Multiple Access)
  - CSMA/CD (CSMA/Collision Detection)
    - 이더넷에서 채용하고 있는 알고리즘

### CSMA/CD 
- Carrier Sense 는 회선을 감시
- 회선에 충돌을 감지
  - 일정 시간 지나고 다시 보내는 방식
- 16번 까지 재시도 가능

# 4. 데이터 링크 계층
## 4-1) 기능
- 노드 대 노드 전달의 책임 (실제로 통신이 되는 것)
- 프레임 구성 : 네트워크 계층에서 받은 비트 스트림을 프레임 단위로 나눔
- 물리주소(MAC) 지정 : 송신자와 수신자의 물리 주소를 헤더에 추가
  - 역으로 Physical -> Newtwork layer 로 넘어갈 때는 헤더를 떼어서 현재 주소와 맞는지를 확인
- 흐름 제어 : 수진자의 수신 데이터 전송률을 고려해 데이터를 전송하도록 제어
- 오류 제어 : 손상 또는 손실된 프레임을 발견/재전송함. 트레일러를 통해 이뤄짐
- 접근 제어 : 주어진 어느 한 순간에 하나의 장치만 동작하도록 제어

## 4-2) 주요 프로토콜 
- 물리 주소를 얻기 위한 프로토콜
### ARP(Address Resolution Protocol)
- 주소를 해석하기 위한 프로토콜
- 논리적인 IP 주소를 물리적인 MAC 주소로 바꾸기 위함
- 캐시를 통해 얻은 정보가 저장. (보통 20분 수명)
### RARP(Reserve Address Resolution Protocol)
- 역 주소 프르토콜
- 저장 장치가 없는 네트워크 단말기 등이 IP 얻기 위해 사용하는 것

## 4-3) 노드 대 노드 (Hop to Hop) 의 전달 책임
- 통신을 위한 PC, 중간에 Route 기능을 하는 Intermediate System, 그리고 종단. 이렇게 각각이 노드
### 물리주소의 데이터 전달 과정
ex) 물리주소 10인 노드가 물리주소 87인 노드에 프레임을 보낸다(07:01:02:01:2C:4B => 물리주소 예시)
- 데이터 링크 수준에서 이 프레임은 헤더에 물리 주소들을 가진다
- 여기서는 이 주소들만 필요
- 헤더의 끝에는 이 수준에서 필요한 다른 정보 존재
- 트레일러에는 보통 오류 검출을 위한 비트들이 포함

# 5. 네트워크 계층 (Network Layer)
## 5-1) 기능
- 패킷을 발신지 대 목적지 전달에 대한 책임
- 발신지 대 목적지 전달
- 논리 주소지정(Logical Addressing)
  - MAC 주소는 디바이스를 식별하기 위한 것
  - 논리 주소는 디바이스를 거쳐서 상대방이 어디에 있느냐를 찾는 것
  - IPv4 와 IPv6 (192.168.3.13 등등)
  - 상위 계층에서 받은 패킷에 발시낮와 목적지의 논리 주소를 헤더에 담는다.
- Routing
  - 패킷이 최종 목적지에 전달될 수 있도록 경로를 지정하거나 교환이 가능
  - 더 하위 계층에서는 Frame 이라고 불렀는데 이 계층에서부터는 패킷이라고 부름   

## 5-2) 네트워크 계층의 주요 프로토콜
### ICMP (Internet Control Message Protocol)
- 에러가 발생했을 때 에러 원인을 알려주거나 네트워크 상태를 진단
### IGMP (Internet Group Management Protocol)
- 호스트(컴퓨터)가 멀티캐스트 그룹 구성원을 인접한 라우터에게 알리는 프로토콜
### IP (Internet Protocol)
- 가장 주된 프로토콜
- 네트워크 기기에서 논리적 식별을 위한 주소
- IPv4 는 약 40억개의 주소
- IPv6 는 2의 128제곱의 개수를 가진 주소 (해변의 모래알을 다 담아도 남아 돈다는 썰)

## 5-3) 발신지 대 목적지 전달 책임
- End To End 전달 책임이 있는 것
- 네트워크 논리 주소는 최종 목적지의 데이터만 가지고 있지, 중간에 거치는 물리주소는 모른다
- 네트워크 논리 주소는 최종 목적지에 도착하기 전까지 바뀌지 않고 물리 주소만 계속 바뀐다


# 6. 전송 계층 (Transport Layer)
## 6-1) 기능
- 전체 메시지의 프로세스 대 프로세스 전달에 대한 책임
- 여기서 프로세스란 앱, 프로그램 등을 의미
- 여기에서 필요한 것은 Port(어떤 프로그램이 구동되고 있는지를 표현) 가 필요
- 프로그램에서 만들어내는 메시지는 한 번에 보낼 수 없기에 잘라서 보내줘야 하는데 그것을 전송 계층이 담당하는 것
- 전체 메시지가 완전하게 바른 순서로 도착하는 것을 보장
- 네트워크 층은 개별적인 패킷의 종단 대 종단 전송을 담당하는 것
### 포트 주소 지정
- 네트워크 계층은 패킷을 정확한 컴퓨터에 전달하는 것
- 전송 계층은 해당 컴퓨터의 정확한 프로세스(프로그램)에 전달하는 것

### 분할과 재 조립
- 데이터를 전달 가능한 Segment 단위로 나눔
- 각 Segment 는 순서 번호를 가지며 이를 재 조립하거나 패킷 손실 여부를 판단하여 버리고 데이터 다시 보내도록 함

### 기타
- 연결 제어
- 흐름 제어
- 오류 제어

## 6-2) 전송 계층의 프로토콜
### TCP (Transmission Control Protocol)
- 연결형 서비스로 가상 회선 방식을 제공
- 신뢰성을 보장.
- 3-way handshaking 과정으로 연결
- 전이중(Full-Duplex), 점대점(Point to Point) 방식
- 보장하는 만큼 느릴 수 있다
### UDP (User Datagram Protocol)
- 비연결형 서비스로 데이터 그램 방식을 제공
- 신뢰성이 낮음
- 그러나 속도는 빠를 수 있다
- 예를 들어 영화는 몇 개의 프레임이 도착하지 않아도 보는데 지장은 없기에 적합
### SCTP (Stream Control Transmission Protocol)
- SCTP 는 UDP 와 TCP 를 적절히 섞어 놓는 것
- 비연결형 서비스에 다중 연결을 지원하는 새롭게 설계된 프로토콜

# 7. 응용 계층 (Application Layer)
## 7-1) 기능
- 사용자가 네트워크에 접근할 수 있도록 해줌
- 사용자 인터페이스를 제공
- 서비스
  - 원격 로그인, 파일 액세스, 전송, 관리, 메일 서비스, Http www 등

## 7-2) 프로토콜 및 프로그램
- FTP : File Transfer Protocol
- Telnet
- SMTP (Simple Mail Transfer Protocol)
  - 메일 관련
- DNS (Domain Name System)
  - 주소를 치면 IP Address 를 가져옴
- Http
- DHCP
  - 동적 IP 할당
- Ping
  - 상대방이 살아 있는지를 보내는 것
- Tcpdump
- Tracerouter