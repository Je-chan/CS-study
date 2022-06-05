# 1. Query Processing in DBMS
## 1-1) Query Processing 
### Parser
- SQL 문장을 분석해서 Syntax 체크 및 catalog 를 이용해서 sematic check 등으로 annotated AST 를 생성한다
- SQL 문이 틀렸을 때 에러를 발생시키는 곳
### Optimizer
- AST 를 분석하여 Execution plan 을 형성한다
- AST 에 따라 가능한 Execution plan 리스트를 만들고 그중에서 최저 비용을 갖는 Execution plan 을 선택한다
- Rule-Based Optimizer
  - 미리 정해 놓은 Rule 에 맞춰서 logical access 경로를 비교하며 최적의 plan 형성
- Cost-Based Optimizer
  - Plan 의 각 실행 Operator 의 cost 를 미리 정해놓고 예상 비용 계산해서 최적의 Plan 을 형성

## 1-2) Query Optimization on DBMS
- DBMS 에서 가장 어려운 파트
- Response time 이 좋고 Throughput 이 좋은 것이 가장 좋은 plan
- 좋은 DBMS 는 query 를 가장 효율적인 Execution plan 으로 변환하여 실행한다
- 어떤 optimizer 도 "optimal" plan 을 만들어낸다고 보장할 수 없다 (이미 증명됨)
  - Real plan cost 를 추측하여 cost estimation
  - 가능한 plan 수를 제한하기 위해 휴리스틱 방법을 사용 
  - 최적의 알고리즘은 어떤 휴리스틱을 사용했느냐
### Cost estimation
- Resource utilization : CPU, Disk IO 개수, network
- Cost of algorithms and access methods
  -  알고리즘과 테이블 어떤 방법으로 Access 를 하는지에 따른 비용
- Size of intermediate results
  - 중간에 쿼리 결과로 여러 조인이 된다면, 어떤 것을 먼저 조인 하느냐에 따라 중간 결과에 나온 Result size 가 다를 것 
  - 중간 Result Size 를 줄여야 한다
- Data Statistics (skew, order, placement)
  - 데이터가 한 쪽으로 몰려 있는지
  - 데이터가 정돈되어 저장이 됐는지
  - distribution, location 등 데이터를 읽어오는 곳이 달라짐에 따라 다른 것

## 1-3) Relational algebra equivalence
- 두 Relational algebra expression 이 같은 결과의 튜플 셋을 만들어 내는 경우 Equivalent 하다고 한다
- Equivalnece Rules 라는 총 11개의 rule 이 있다 
  - 그 rule 에 맞춰 candidate plan 을 만들고 가장 cost 가 낮은 것을 사용한다
