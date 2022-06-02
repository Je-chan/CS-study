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
