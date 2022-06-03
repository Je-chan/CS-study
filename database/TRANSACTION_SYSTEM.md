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
