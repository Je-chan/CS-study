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

# 2. Selection Operators
## 2-1) Selection Strategies

### <1> Linear Search
- Full table scan
- 데이터가 저장된 모든 disk block 을 읽어서 하나하나 스캔하는 것
- Index 가 없는 경우

### <2> Binary Search (B+ Tree Idnex)
- Exact Matches : 정확하게 Value 를 찾아내는 것
- Multiple Matches : 정확하게 여러 Value 를 찾아내는 것
- Range Queries : between 과 같이 Query 의 결과가 Range 인 것
- Complex Queries : Range 보다 더 복잡한 식을 포함하고 있는 것. 단지 하나의 결과만 뽑아 내는 것이 아닌 경우

### Index 를 이용한 Search 
- Index access 를 위한 disk block access 와 실제 data block access 가 필요하긴 함
- 하지만 일반 query 에서 전체 full table scan 에 비해 access 횟수가 훨씬 적다

## 2-2) Join Strategies
- Join의 경우, 세 가지 형태로 Execution Plan 이 만들어진다.
### <1> Nested Loop join
- 조인을 순차적으로 수행하면서 데이터에 random access 
- inner table 에 조인을 위한 index 가 필요 
- 비용 : Outer table 카디널리티 * Inner table 의 access count

### <2> Merge Join (Sort Merge Join)
- 데이터에 Random Access 하지 않고 스캔을 수행
- 양쪽 테이블 처리 범위를 각자 접근해 정렬한 결과를 차례대로 스캔
- 인덱스가 존재하지 않고 Exact 가 아니고 조인 연사자가 "=" 가 아닌 경우 좋은 성능

### <3> Hash Join
- 내부적으로 hash 테이블을 만들어서 수행
- 테이블을 새롭게 생성하는 비용이 발생하기에 한 쪽 테이블이 작을 때 사용한다
- 두 테이블 중 작은 테이블에 대해 hash 테이블 생성 
- 큰 테이블을 스캔하면서 hash table 을 조회하며 join
- hash table 생성 cost 가 추가되기에 join 대상 테이블이 작을 때 유리 

# 3. Optimizer
- Optimizer 방식은 Bottom-Up(B-U) 과 Top-Down(T-D) 두 가지로 나뉜다. 
  - Bottom-Up
    - 아무것도 없는 상태에서 시작해 plan 을 시작부터 만들어 가면서 원하는 최종 plan 을 만드는 형식
    - System R, Starburst 등
  - Top-Down
    - 최종 Logical plan 을 갖고 plan tree 를 내려가며 alternative plan 과 비교하며 cost 를 계산하고 optima plan 을 계산
    - Volcano, Cascades 등

## 3-1) Heuristic-based Optimization (B-U)
- Logical Operator 를 Physical plan 으로 바꾸는 Static Rule 을 이용
  - 가장 제한적인 Selection 먼저 수행
  - Join 전에 모든 Selection 수행
  - Predicate / Limit / Projection 은 Selection 으로 모두 Push down
    - Prdicate 는 가장 먼저 하는 것이 이론상 효율적
  - 카디널리티를 기준으로 Join Ordering

## 3-2) Heuristic + Cost based Optimization (B-U)
- 기존의 Static Rule 로 Initial Optimization 수행
- 그 다음 좋은 Join Ordering 을 결정하기 위해 dynamic programming 수행
  - 첫 번째 cost-based optimizer
  - divide and conquer 방식을 이용한 Bottom-up planning
- Bottom-Up 에서 가장 많이 사용하는 방식
- System R, 초기의 IBM DB2 등 대부분의 오픈 소스 데이터베이스들이 이 방식을 사용
### System R Optimizer
- query 를 blocks 로 나누고 각 block 을 위한 Logical operator 들을 생성
- 각 logical operator fmf dnlgks physical operator 의 모든 가능한 join path 와 access path 의 조합을 set 으로 생성
- 반복적으로 cost 를 계산하면서 가장 cost 가 작은 Plan 의 left-deep tree 를 만든다

## 3-3) Volcano Optimizer (T-D)
- 관계 대수의 equivalence rule 을 기본으로 하는 cost-based optimizer
- 핵심은 먼저 plan 을 빠른 방법으로 만들어 놓고 alternative 방식을 찾고 바꿔가면서 좋은 plan 을 찾는 것
- 새로운 operator 와 equivalence rule 을 추가하기 쉽다
- branch and bound 방법을 사용해 top-down 방식으로 optimal plan 을 고른다
- 일단 logical plan 에 대한 가능한 physical plan 을 고르고 그 다음 alternatice plan 을 찾아 비교한다
- Memo table 을 이용하여 equivalent operator 를 저장한다

## 3-4) Cascade optimizer (T-D)
- Object-oriented implementation of Volcano 
- SQL server 에서 사용하는 중

## 3-5) 결론
- Optimizer 에 의해서 query optimization 은 매우 어려운 문제
- 알고리즘으로 완벽하게 구현한다는 것은 거의 불가능
- DBMS 는 휴리스틱과 지정된 코스트를 가지고 optimal 하다고 할만 한 plan 을 찾는 것
- DBMS 는 Optimizer 의 역할을 보완하기 위한 기능을 제공
  - SQL Hint (힌트에 맞춰서 plan 을 짤 수 있도록 하는 것)
  - Plan Stability (저장된 old 버전의 plan 을 사용. 새로운 plan 이 예전의 plan 보다 좋은 것은 아님. trade-off 된 것뿐)