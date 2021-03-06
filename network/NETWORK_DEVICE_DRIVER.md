# 1. 이더넷 프레임과 길이
## 1-1) NIC (Network Interface Controller) 
- 네트워크 장비와 LAN 사이의 통신 준비
- 전송될 데이터를 병려에서 직렬로 전환
- 빠른 전송을 위해 데이터를 코팅, 압축함
- 목적지 장비의 NIC 는 데이터를 수신 후 CPU 로 데이터를 전달하는 역할을 담당
- Access control 기능이 구현된 하드웨어와 펌웨어가 존재
  - 하드웨어라 하면 칩 안에 있는 저장소
  - 제어를 위한 제어 레지스터와 저장하기 위한 저장 레지스터가 존재
- 기본은 유선이나 무선으로 설계될 수 있음
- 이더넷, 기가비트 이더넷, 광섬유, 토근링 등등이 존재

## 1-2) 네트워크 칩 예시 (CS8990A)
- 속도 : 10Mbps(bits/sec) 를 지원하는 이더넷 컨트롤러
- IEEE 802.3 기준 (이더넷 표준을 다루는 IEEE) 
- 10BASE-T, 10BASE2, 10BASE5, 10BASE-F 지원하기 위한 AUI whswo
- EEPROM 제공 (MAC 주소가 담겨 있음 => 제조사도 알 수 있음)
- CSMA/CD 존재 (충돌 처리)

### CSMA/CD 기본 알고리즘
- 데이터 있음
- -> Carrier sensing : 채널이 사용중인가? (yes => backoff)
- -> 전송
- -> Collision detection : 충돌 발생 ? (yes => jamming signal 전송 => backoff)
- -> 전송 끝

- 이외에도 Two-Part Deferral, Simple Part Deferral 이 존재

## 1-3) 이더넷 계층 구조
- Data link Layer 에서 존재
  - LLC (Logical Link Control) : 흐름제어와 오류 제어 담당
  - MAC (Media Access Control) : CSMA / CD 접근 방법에 대한 동작을 담당
- 칩의 설계에 따라서 Physical Layer 까지 모두 담당할 수 있음
 
## 1-4) 이더넷 프레임 포멧
### 패킷
- Preamble
- SFD : Start of Frame Delimiter, 프레임을 시작하기 위함
아래부터 Frame
- DA(6바이트) : Destination Address, 도착 주소
- SA(6바이트) : Source Address, 전송 주소
- Length Field(2바이트)
- LLC data
  - Logical Link Control
- Pad
- FCS(4바이트)
  - Frame Check Sequence
  - 꼬리 부분. 에러 체크 같은 것을 하기 위함

- 프레임은 최소 64 바이트에서 최대 1518 바이트
### 프레임 Encapsulation 과 Decapsulation 중 전송 시,
-  한 번에 보내는 적절한 프레임 바이트 수가 CS8900A 메모리에 전송되고 네트워크에 접속할 수 있게 함
- MAC 은 SFD(10101011b 등) 뒤에 7바이트의 preamble(1010101b 등) 을 전송한다
- SFD 와 FCS 사이의 데이터는 host 에서 공급
  - CS8900A 가 제공하는 FCS 생성기는 InhibitCRC bit 가 Set 됨에 따라 비활성화된다. 

### 프레임의 길이
- Preamble(7 byte)
- SFD 
이더넷 헤더
- DA(6 byte)
- SA(6 byte)
- Length(2 byte)
데이터
- Data (46~1500 바이트로 가변적)
  - DSAP
  - SSAP
  - CTRL
  - DATA
꼬리
- FCS (4byte)


# 2. 레지스터와 네트워크 드라이버
## 2-1) 레지스터 기본 접근 방법
- PacketPage Pointer Port 를 통해 접근
- 기본 주소 (base address) + 0X000A(ADD_PORT) 를 더해서 결정
- 레지스터에 있는 데이터를 읽기 위해서는 이 PacketPage Pointer Port 에 먼저 레지스터의 offset 을 쓰고, data port 를 통해서 읽어 온다
- 쓰는 것도 PacketPage Pointer Port 에 먼저 레지스터의 offset 을 쓰고 나서, data port에 쓸 내용을 적어서 처리

## 2-2) Packet Page Address
아래의 주소들을 사용해서 디바이스 칩에 접근한다
- 0000h ~ 0045h : Bus Interface Register
  - ISA 버스를 이용해 호스트 시스템 혹은 I/O 메모리에 맵핑
  - 초기 설정 후 작동 중에는 변경하지 않음 
- 0100h ~ 013Fh : Status and Control Register
  - 레지스터의 상태를 얻거나 주요 제어를 담당
- 0140h ~ 014Fh : Initiate Transmit Register
  - 이더넷 프레임을 전송할 때 초기화 시 사용
- 0150h ~ 015Dh : Address Filter Register
  - 원격 주소 필터에 의해 사용
- 0400h : Receive Frame Location
  - 이더넷 프레임을 호스트로부터 전송할 때 사용
- 0A00h : Transmit Frame Location
  - 마찬가지로 호스트로부터 전송할 때 사용

## 2-3) Network Protocol Stack
### Low -> High 순
- Ethernet H/W (Physical)
Kernerl (OS가 제공)
- Network Device Driver
  - => IP
  - => TCP / UDP
  - => INET socket Interface
    - 인터넷 기기종간의 통신
  - => BSD socket Interface
    - 로컬 네트워크로 사용하는 옵션 인터페이스
Application
- Applciation 

## 2-4) 커널 부팅과 네트워크 디바이스 드라이버
- 커널 초기화 과정
  - 네트워크 디바이스 드라이버가 커널에 올라가 동작 대기
- 등록된 네트워크 디바이스 드라이버 확인 (Linux)
  ```shell
  cat /proc/net/dev
  ```
- ifconfig 명령
  - 네트워크 디바이스를 활성화 하거나 비활성화
```shell
ifconfig [interface] [ip] [netmask mask] [up | down]
```

## 2-4) 네트워크 요소
- 인터페이스 이름
  - lo : 루프백 Address. 127.0.0.1 사용을 위한 논리적인 소프트웨어 주소
  - dummy0, eth0 등
- 인터페이스 타입
  - ethernet, isdn, adsl 과 같은 네트워크 형태
  - ifconfig -a 명령 시 Link encap 항목에서 확인
- 하드웨어 주소
  - 이더넷 하드웨어 MAC 주소
- 네트워크 주소
  - TCP/IP 에서 사용을 위한 IP 주소
- 데이터 처리 능력
  - 한 프레임을 전송할 수 있는 데이터량을 MTU라고 함. 패킷을 전송할 때 분할 기준이 됨
  - 송신 시 네트워크 디바이스가 바쁠 때, 시스템에 대기할 수 있는 큐의 크기는 txqueuelen 으로 표기됨

# 3. 소켓과 시스템콜의 흐름
## 3-1) 네트워크 소켓
- 네트워크는 소켓으로 관리
- 소켓이라는 단일한 인터페이스로 묶어서 사용
- 운영체제 입장에서 보면, 디바이스를 통과해서 프로토콜 스택을 통과한 다음 사용자 계층에서 프로그래밍 하기 위한 구조가 필요. 
  - 그것을 인터페이스라 하고 편하게 소켓이라는 이름을 사용함
- 소켓 인터페이스를 통해서 하위의 네트워크에서 일어나는 일들을 간략하게 함수를 통해서 접근.
- 네트워크를 초기화하여 다양한 통신 수단을 사용한다
- 운영체제의 파일시스템과 연동하여 구성
- 상위에서 보면 파일처럼 다루게 됨
- 소켓에서는 socket(), bind() 라는 시스템콜 함수를 이용
  - 시스템콜이란 유저 계층에서 운영 체제(OS) 내부에 있는 특정 요소를 실행시키 위한 것
  - 시스템 콜을 통해서 내부의 특정 함수에 진입을 하고 프로토콜 스택에서 존재하는 디바이스 드라이버 구현부에 접근
- 눈에 보이는 건 소켓 인터페이스  
  - 그래서 대부분 소켓 인터페이스는 표준으로 만들어지고 난 다, 하위 호환성 유지를 위해 해당 인자는 동일
  - 운영체제는 계속 발전
  - Linux 는 오픈소스 답게 굉장히 빠르게 버전 발전을 이루는 중

## 3-2) 소켓의 구현 정보
### 정의
- 소켓 프로그램에 있어서 로컬 호스트에서 실행되는 프로세스와 원격 호스트에서 실행되는 프로세스 간에 데이터 교환을 위해 요구되는 정보
### 종류
- 프로토콜 식별자, 로컬 인터넷 주소, 로컬 포트 번호, 원격 인터넷 주소, 원격 포트번호로 구성

### 구현
- 네트워크 드라이버는 주소 설정, 전송 매개변수 변경, 소통량과 오류통계 유지와 같은 상당수의 관리 작업을 지원하도록 설계
- 네트워크 드라이버에는 쓰거나 읽거나 하는 함수가 존재
  - 네트워크 칩에 들어가서 레지스터에 접근하는 것
  - 그곳에 해당하는 주소ㅗ 설정, 매개 변수 등을 지원하기

## 3-3) 리눅스의 구현된 초기화 함수들
### 새로 작성하지 않는 경우 
- ether_setup : 이더넷 인터페이스 초기화 함수
- fc_setup : 광채널 인터페이스 초기화 함수
- fddi_setup : 광통신 인터페이스 초기화 함수
- hippi_setup : 고성능 병렬 인터페이스 초기화 함수
- ltalk_setup : 애플토크 인터페이스 초기화 함수
- tr_setup : 토큰링 인터페이스 초기화 함수

### 할당과 셋업을 동시에 할 경우
- struct net_device *alloc_etherdev(int sizeof_priv) : 이더넷
- 등등

## 3-4) Layer Model : data structure level
### device layer
- struct device
### network layer
- struct tcp_func
- struct packet_type
### transport layer
- struct tcp_opt
- struct proto
### inet layer
- struct sock
- struck proto_ops
### BSD socket layer
- struct net_proto_family
- struct socket
### VFS layer 
- struct file_operations
- 최종적으로 Virtual File System 이라고 하는 파일처럼 다루는 Layer 가 존재
- 실제 파일은 아니지만 파일이라는 형태로 취할 수 있도록 추상화 하는 것

## 3-5) Socket Buffer
- 보낼 데이터는 버퍼로 저장돼 있음

### <linux/skbuff.h> 에 정의된 sk_buff 구조체의 주요 필드
- struct net_device *dev;
  - 소켓 버퍼는 버퍼를 주고 받을 dev 를 지정하는 필드를 가지고 있다
- unsigned char *head
- unsigned char *data
- unsigned char *tail
- unsigned char *end

- 사용 가능한 주소 영역 : skb->end - skb->head
- 사용중인 자료 영역 : skb->tail - skb->data
