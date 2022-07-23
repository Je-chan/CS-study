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