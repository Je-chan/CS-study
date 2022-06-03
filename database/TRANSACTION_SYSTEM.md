# 1. Transaction

## 1-1) 데이터 무결성

- 트랜젝션의 목표가 바로 데이터 무결성
- 데이터 무결성을 유지하기 위해 해결해야 할 문제들
  - Atomic Operation : 각각의 Opretion 이 독립적
  - Concurrency Control (병행 제어)
  - 장애 후 Recovery : 장애가 발생했을 때 그 시점으로 정확하게 돌아가야 한다
- Consistent database state
  - 데이터 무결성이 유지되어 데이터베이스 안의 데이터 간 모순이 없는 상태
  - 일시적으로 inconsistency 가 발생할 수는 있지만, 결국 일관된 값으로 유지돼야 한다

## 1-2) 트랜잭션

- 데이터 무결성을 지켜 데이터베이스를 일관된 상태(Consistent State)로 유지하기 위한 핵심 개념
- 그냥 트랜잭션이라고 한다면 다음의 Data processing 의 집합을 포함한 Atomic Operation 이라고 보면 된다 (일의 한 단위)
  - 하나 이상의 READ Operation
  - 하나 이상의 WRITE Operation
  - 트랜잭션 안에서의 데이터 계산 Operation
- 트랜잭션을 어떻게 Handle 하냐에 따라 DBMS 가 Consistent State 를 유지하냐가 결정된다.

### ACID

- Atomicity : 트랜잭션 안의 모든 Opration 이 실행되거나, 모두 실패
- Consistency : 트랜잭션의 실행이 Database 의 무결성을 유지
- Isolation : 동시에 실행되는 트랜잭션은 서로 독립적 혹은 순차적 진행
- Durability : 성공적으로 수행된 트랜잭션은 영원히 반영돼야 함

<img src="https://beginnersbook.com/wp-content/uploads/2018/12/DBMS_Transaction_States_diagram.png" alt="DBMS Transaction States">

- TRANSACTION 순서
  - 처음 트랜잭션이 시작하면 ACTIVE 상태
  - READ, WRITE Operation 수행
  - (1) 만약 Operation 이 성공하면 Partially Commited 라고 한다 (일시적)
    - (a) 만약 Commit 이 된다면
      - Commited 돼서 영구적인 Change 가 발생
      - (bTermianated. 종료
    - (b) 만약 Commit 실패했다면
      - SQL Rollback 후, Failed 상태로 가서 Rollback 이 Trigger
      - Aborted 되고, 이때 메모리상에 갖고 있던 Temporal 했던 것까지 다 지운다
      - 트랜잭션 시작 전으로 다 되돌린다
      - Terminated 종료
  - (2) 만약 Operation 이 실패하면 Failed 상태로 들어간다
    - 이후 (1)(b) 와 동일한 과정을 거침

# 2. 트랜젝션 스케쥴(Transaction Schedule)

## 2-1) 트랜젝션 스케쥴이란?

- 데이터베이스의 Consistent State 를 유지하기 위해 동시에 실행되는 트랜젝션들의 Operation 순서를 정하는 것을 의미
- 스케쥴링이 어떻게 되느냐에 따라서 결과값이 달라진다

### Serial or Serializable Schedule

- 두 스케쥴은 정확한 결과를 만들어낸다.

Serial Schedule

- 한 트랜잭션의 모든 Operation 이 끝난 후 다른 트랜잭션이 시작
- Serial Schedule 은 항상 Consistent 결과를 생성
- 그러나, 순차적으로 실행하면 Throughput 이 좋지 않다

  - 동시에 실행하지 못하기에 DBMS 의 강점이라고 할 수 있는 Concurrency (동시 실행) 이 안 된다는 것

Serializable Schedule

- 스케쥴이 Serial Schedule 과 Equiavalent 할 때 Serializable 이라고 한다
- Serializable 을 계산하기 위한 두 가지 방법이 존재
  - Conflict Serializable
  - View Serializable

## 2-2) Conflict Serializability

### Conflict Operations

- 두 개의 Operation이 다른 트랜젝션에 속해 있고
- 같은 데이터에 대한 Operation 이며
- 적어도 하나의 Operation 이 Write 인 경우

### Conflict Serializability

- non-conflict operation 들의 순서를 바꾸어 serial schedule 로 변환이 되는 스케쥴

## 2-3) View Serializability

### View Equivalnet

- 다음 조건들을 만족할 때 스케쥴 S1, S2 가 view equivalent 하다고 한다
  - Initial Read : 만약, S1의 T1 이 초기값을 읽었다면 S2의 T1 도 초기값을 읽어야 한다
  - Same Read : 만약 S1 에서 T1 이 T2가 Write 한 값을 읽는다면, S2 에서도 T1 이 T2 가 Write 한 값을 읽어야 한다
  - Final Wirte : 마지막 Write 는 같은 트랜잭션에서 이루어져야 한다

### View Serializable

- Serial 스케쥴과 View Equivalnet 한 스케쥴
- conflict serializable 인 스케쥴을 포함
- 그렇기에 View Serializable 하다고 해서 항상 Conflict Serializable 한 것은 아니다
  - Blind Write 가 존재할 수 있기 때문

## 2-4) Precedence Graph

- 스케쥴의 Conflict Serializability 를 테스트할 때 사용.
- 트랜잭션간에 conflict 이 있는지 확인하고, conflict 이 없는 스케쥴이라고 하면 실행. DBMS 는 이런 그래프를 계속해서 Management 하고 있다
- 각 트랜잭션이 graph 의 노드
- 만약 트랜잭션간에 conflict 이 있을 때, directed edge 로 표시
  - T2 가 WRITE 하기 전에 T1 이 READ
  - T2 가 READ 하기 전에 T1 이 WRITE
  - T2 가 WRITE 하기 전에 T1 이 WRITE
- 그래프에 cycle 이 없다면 conflict serializable 스케쥴
- 그래프에 cycle 이 있다면 무조건 Serial 하게 스케쥴을 진행해야 한다

## 2-5) 복구 가능한 스케쥴(Recovrable Schedule)

- 스케쥴은 Serializable 해야 하고 복구 가능해야 한다
- T2 가 T1 에 의해 바뀐 데이터를 사용할 경우, T1 이 커밋되기 전에 T2는 커밋하면 안 된다
  - 만약, T1 이 Abort 되면 T2 도 Abort 돼야 하기 때문
- T2 이 T1 가 Write 한 것을 읽는다면 T1 에서 Commit Operation 은 T2에서의 Commit Operation 보다 먼저 실행돼야 한다

### Cascade-less Schedule

- T1 -> T2 -> T3 -> T4 이 순서로 데이터가 사용될 때, 만약 T1 이 실패하면 모든 트랜잭션 실패
- Cascading Schedule 은 복구 불가능한 스케쥴을 만든다.
- 그렇기에 Cascade-less 한 스케쥴이 Recoverable 한 스케쥴이 된다
  - 다른 트랜잭션들은 commit 후에 변경된 데이터를 읽는다는 것

# 3. 회복 (recovery) 시스템

## 3-1) Crash Recovery (회복)

- Recovery 란 시스템에 장애가 일어나고 DBMS 가 다시 실행됐을 때, 그 상태가 Consistent 하게 만드는 것
- Recovery 는 다음의 트랜잭션 특성과 관계가 있다
  - Atomicity : 트랜젝션의 Operation 들이 실행 중에 장애를 발생시키면 실행된 Operation Rollback
  - Durability : 장애 후에도 Commit 된 트랜젝션의 데이터는 영원히 저장
  - 즉, 장애가 발생했을 때 커밋이 되지 않은 건 Rollback, 커밋된 건 계속 저장된 상태로 남아야 한다

## 3-2) Logging

- DBMS 에서 Recovery 는 Logging 을 통해서 가능해진다
- log는 데이터베이스의 변경 내용을 저장하고 있는 파일. DBMS 는 Database 에 데이터를 저장하기 위해 Disk Tablespace, Checkpoint Images, Log 이렇게 갖고 있다.
- 데이터베이스가 크래시가 나서 내려갔다가 다시 올라오면 Disk Tablespace 에 있는 것들을 읽으면서 Checkpoing로 올리고 Log 를 읽으면서 리플레이를 할 것인지 등 Recovery 과정을 거쳐서 데이터베이스가 Active 해지면 Consistent State 를 유지하는 것

### Aries Recovery Algorithm

- 가장 많이 쓰는 것. 현재 모든 DBMS 가 이 방식을 따르고 있다
- WAL(Write Ahead Log)
  - 디스크에 변경된 내용을 적기 전에 반드시 로그가 안전한 Storage 에 저장돼야 한다
  - 변경된 내용을 디스크에 log record 를 먼저 기록해야 한다 (Atomicity 보장)
  - Commit 전에 모든 log record 들이 저장돼야 한다 (Durability 보장)

### UNDO Log

- Crash 후에 commit 되지 않은 변경 사항들을 UNDO(원복) 할 때 사용
  - 모든 액션은 UNDO Log Record 에 생성 (old 값을 포함)
  - Disk 에 업데이트 하기 전 Log Record 를 Disk 에 적음 (WAL 방식)
  - Commit 로그가 flush 되기 전 트랜젝션의 모든 체인지를 Disk 에 반영
  - Commit 로그가 반영된다는 게 Durability 를 반영했다는 의미

### REDO Log

- Crash 후에 commit 된 변경 사항들을 REDO (replay) 할 때 사용
  - 모든 액션은 REDO Log Record 를 생성 (new 값을 포함)
  - Disk 에 업데이트 하기 전 Log Recored 를 Disk 에 적음 (WAL)
  - 모든 Log Record 를 commit 할 때 flush
  - 체인지가 Disk 에 반영된 후에 End Log Record 를 Write

## 3-3) Checkpoint

- REDO Log 를 실행할 때 처음 로그를 찍은 시점부터 시작을 하면 시간이 오래 걸린다
- 따라서 DBMS 는 주기적으로 스냅샷이라고 하는 Checkpoint를 생성. 이전의 로그들은 Truncate 하거나 백업
- 시스템 충돌 시 복구에 걸리는 시간을 최소화 한다.

### Fuzzy Checkpoint

- Checkpoint 하는 동안 다른 Operation Block 을 오래 하는 것을 막기 위해 Checkpoint 할 때 UPDATE 를 허용하는 방식

# 4. 병행 제어(concurrecy)

## 4-1) Lock Protocol

### Locking Protocol in DBMS

- 동시에 실행되는 트랜젝션 간 Serializable 스케쥴을 만들어 주는 메커니즘
- Lock 을 획득하기 전에 데이터에 읽거나 쓰는 동작을 할 수 없게 해서 동시성 문제를 해결한다
- Lock-Manager in DBMS
  - 모든 Lock 에 대한 Request 처리
  - Lock 에 대한 정보(어떤 트랜젝션이 어느 Object 에 대한 Lock 을 갖고 있는가)를 Hash Table 에 저장하여 관리
  - 내부적으로 Lock Manager 가 Hash table 로 Lcok 의 상태를 Request 가 있을 때마다 업데이트
- Lock 종류

### Lock 의 종류

- Shared Lock
  - READ ONLY Lock
  - READ 트랜젝션 간에 Share 가 가능
  - Write 를 위한 Exclusive Lock 인 경우 Block
- Exclusive Lock
  - READ WRITE Lock
  - 딱 정확하게 이것에 대해서는 나만 접근할 수 있다의 컨셉
  - 대상이 되는 아이템을 베타적으로 Hold 한다
  - READ / WRITE 모든 Operation 을 Block 시킨다
- Lock 은 트랜젝션이 커밋될 때 Release 한다

### Dead Lock

- 여러 트랜젝션들이 서로 각각의 Lock 을 잡고 상대 트랜젝션이 잡은 Lock 을 잡아서 무한정 기다리게 되는 상황
- DBMS 의 Lock Manager 는 deadlock detect 하고 dedlock이 되는 트랜젝션을 롤백한다

### Two phase locking protocol

- Serializable 스케쥴을 만들기 위해 Lock 의 acquire / require 순서를 정하는 프로토콜
- Growth Phase
  - Operation 하기 전에 필요한 Lock 을 다 잡는다
  - Lock Acquire 하는 단계
- Shrinking Phase
  - Operation 이 끝났을 때 Lock 을 Release 해야 한다.
  - Lock release 하는 단계

### MVCC

- Locking 에서 오는 성능 문제를 개선한다
- 위의 방법들은 옛날 방식. MVCC 는 많은 DBMS 에서 현재 사용하는 동시성 제어 기법
- READ 에서 Shared Lock 을 잡지 않는다. MVCC 를 지원해서 Locking 에서 오는 성능 문제를 해결한다
- 데이터 업데이트가 있을 때 이전의 데이터를 덮어 씌우는 게 아니라 새로운 버전의 데이터 버전 Space 를 저장하고 Link 를 생성한다
- 데이터를 조회할 때 트랜젝션의 시작 시점에 맞는 데이터 버전을 반환한다
  - READ Operation 은 Lock 을 하지 않으므로 WRITE 도 그만큼 빨라진다
  - Lock Protocol 을 사용하는 DBMS 보다 더 좋은 성능을 보인다
- 버전에 대한 garbage collection 이 필요하긴 함
  - 버전이 있을 때마다 Link 가 있어서 Clean up 을 해줘야 하기 때문
