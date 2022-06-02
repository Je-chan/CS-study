# 1. 정규화의 개념

## 1-1) 정규화란?

### 정규화의 개념

- 관계형 데이터베이스의 설계에서 중복을 최소화하기 위해 데이터를 구조화하는 프로세스
- 이론적으로 들어가면, 함수적 종속성을 이용해서 연관성 있는 속성들을 분류하여 각 릴레이션들에서 이상현상이 생기지 않도록 하는 과정
- 단순하게 설명하면 큰 테이블을 연관 있는 컬럼들로만 쪼개서 테이블을 작게 만들어가는 과정

### 정규화의 목적

- 불필요한 데이터를 제거, 데이터 중복을 최소화
- 데이터베이스 구조 확장 시 재 디자인을 최소화
- 무결성 제약조건의 시행을 간단하게 하기 위함
- 이상(Anomaly)현상을 방지하기 위해 테이블 구성을 논리적이고 직관적으로 만들고자 함

## 1-2) 이상 현상

- 아래와 같은 이상 현상들이 발생하는 원인은 관련 없는 속성들을 다 모아서 하나의 릴레이션으로 만들었기 떄문
- 그렇기에 정규화를 해서 관련 있는 속성들로만 구성된 작은 여러 릴레이션으로 분해(Decomposition)해야 한다
  - 속성들 간의 관련성 판단 기준이 함수의 종속성 (FD, Functional Dependency)
  - 일반적으로 하나의 릴레이션에는 하나의 함수적 종속성만이 존재하도록 정규화를 한다

### <1> 삽입 이상

- 데이터를 저장할 때 원하지 않는 정보가 함께 삽입되는 경우
- 컬럼 하나가 빠지는 경우 등.

### <2> 갱신 이상

- 중복된 튜플 중 일부의 속성만 갱신시켜 정보의 모순성이 발생하는 경우 (Consistency 깨지는 경우)
  - User A 가 B 라는 컬럼의 값을 X, Y 두 개 가지고 있다고 하자
  - B 의 값을 Z 로 바꾸고 싶은데 X, Y 중 어떤 것을 해야하는지 모순성이 생기게 된다

### <3> 삭제 이상

- 튜플을 삭제하는 와중 유지돼야 하는 정보까지도 연쇄적으로 삭제되는 경우

## 1-3) 함수의 종속성(Functional Dependency)

### 함수의 종속성이란

- 릴레이션의 여러 Attribute 간의 관계를 표현하는 것
- 주로 기본키와 다른 Non key Attribute 들 사이의 관계를 표현한다
  - X -> Y
  - 여기서 X 가 결정자(Determinant), Y는 종속자(Dependent)
  - X, Y 는 Relation 의 Attribute 이고, X에 의해서 Y가 결정된다고 보면 된다
  - Inference Rule (추론 규칙) 로 함수의 종속성을 파악한다 (이론적인 이야기)
- 함수적 종속성의 분석을 통해 정규화를 실행한다

### 추론 규칙 (Inference Rule)

- Reflexive Rule

  - X -> Y
  - 만약 Y가 X에 포함되는 경우
  - Eg) X = {a, b, c, d, e}, Y={a, b, c}

- Augmentation Rule

  - 만약 X -> Y 이고, Attribute set: Z 가 있다면, XZ -> YZ
  - Eg) R(a, b, c, d) 인 경우, if a -> b THEN {a, c} -> {b, c}

- Transitive Rule

  - If X -> Y AND Y -> Z, THEN X -> Z

- Union Rule

  - If X -> Y AND X -> Z, THEN X -> {Y, Z}

- Decomposition Rule

  - If X -> {Y, Z} THEN X -> Y AND X -> Z

- Pseudo transitive Rule
  - If X -> Y AND YZ -> W THEN XZ -> W

# 2. 정규형의 종류

- UNF(Unnormalized Form) 을 정규화 해서 만든 것이 정규형
- UNF 는 기본키를 가지고 있고, 튜플을 식별할 수 있어야 한다

## 2-1) 제 1 정규형(1NF) : First Normal Form

- 릴레이션에 속한 속성들은 원자값을 갖도록 해야 한다
  - Subject 가 여러 값을 갖고 있다면 다른 릴레이션으로 분리해야 한다
  - 릴레이션의 기본키를 분리된 릴레이션의 외래키로 추가한다

## 2-2) 제 2 정규형(2NF) : Second Nornal Form

- 1NF 를 만족하고 기본키가 아닌 속성들은 기본키의 일부가 아닌 기본키 전체에 함수적 종속해야 한다
  - 키에 속하지 않는 나머지 속성들은 다 Primary key 에 함수적 종속성에 맞아야 한다
  - 부분적으로 종속되는 경우를 나눠야 한다.
- Title 에 종속적인 컬럼만 가진 릴레이션과 Format 에 종속적인 릴레이션을 분리해야 한다

## 2-3) 제 3 정규형(3NF) : Third Normal Form

- 2NF 를 만족하고 이행적 종속(Transitive Functional Dependency) 가 없어야 한다
- 즉, 추론 규칙에서 Transitive Rule 과 관련된 것들은 쪼개야 한다는 것
  - 함수적 종속 관계가 슈퍼키에서 시작하거나
  - prime Attribute(candidate key 에 포함된 Attribute) 로 끝나야 한다
- 3NF 을 만족했다면 정규화 됐다고 한다.

### 제 3.5 정규형 (BCNF) : Boyce-Codd Normal Form

- 3NF 에서 조금 더 강화된 버전
- 모든 함수 종속 관계에서 X 로 가능한 집합의 속성이 모두 슈퍼키이면 BCNF 를 만족한다

### 역 정규화 (Denormalization)

- 정규화가 잘 되면 OLTP, Insert Update Delete 에서는 좋은 모습을 보이지만
- Analytic Query 를 주로 실행하는 OLAP 애플리케이션인 경우 비용이 많이 든다
  - 이런 경우 성능 향상을 위해서 낮은 수준의 정규화를 추구하기도 한다.
  - 일부 쓰기 성능의 손실을 감수하고 데이터를 묶거나 데이터의 복제 사본을 추가
  - 데이터베이스의 읽기 성능을 개선하려고 시도

### 제 4 정규형 (4NF)

- BCNF 를 만족하고 릴레이션의 모든 Nontrivial multi-valued dependency X ->-> Y 의 경우 4정규형에 맞다고 한다

- Multi-valued dependency (X ->-> Y )

  - 함수적 종속성과 달리 이 조건은 튜플이 생성될 때 속성들 사이의 관계를 의미
  - 릴레이션에 3개 이상의 속성이 있을 때 존재한다.
    - X 값이 Y 값의 집합과 Z 값의 집합을 갖는다
    - Y와 Z는 서로 독립적이다

- Trivial multi-valued dependency

  - If Y is a subset of X
  - OR X U Y is while set of Relation

### 제 5 정규형 (5NF, PJNF) - Project Join Normal Form

- Join Dependency : 릴레이션 T 의 부분 속성 집합(projection table of T) {A, B, C} 를 조인한 결과가 릴레이션 T와 같을 때 JD(Join Dependency) 를 만족한다고 한다
- 제 5 정규형의 조건
  - 릴레이션에 존재하는 모든 Join Dependecy 가 기존 릴레이션의 슈퍼키를 가질 때 만족한다
  - 여러 릴레이션으로 분해했을 때 나눠진 릴레이션이 기존 릴레이션의 슈퍼키를 가지고 더이상 무손실 분해가 불가능할 때 만족한다

### 제 6 정규형

- Primary key 와 단 한 개의 속성만 갖는 경우
- 하나의 Entity 를 표현하기 위해 많은 테이블이 필요하고, 데이터 업데이트할 때 여러 테이블을 함께 업데이트 하기에 OLTP 에 좋지 않다
- Analytics Query(OLAP) 을 중시하는 datawarehouse 솔루션들은 내부적 저장 포맷을 column-based 로 해서 6NF 와 비슷한 포맷으로 저장한다
  - Aggregation 과 같은 range query 에 빠른 성능
  - Compression 이 잘 되어 저장 공간을 절약

# 4. 데이터베이스 설계 프로세스 총 정리

## 4-1) 설계 프로세스

데이터베이스 디자인을 통해 테이블 스키마를 다음과 같은 세 방법으로 생성한다

### 기본적인 방식

- E-R 다이어그램을 릴레이션으로 변환하여 생성
- 모든 Attribute 을 포함하는 하나의 릴레이션 생성 (Universal Relation)
  - 이를 정규화하여 작은 테이블로 분해한다
- ad hoc 디자인으로 생성

  - 정규화를 통해 테스트하고 좋은 디자인으로 변화

### E-R 다이어그램과 정규화

- E-R 다이어그램이 모든 데이터를 잘 표현할 수 있게 설계돼 있다면 추가적인 정규화 과정이 필요하지는 않다
- 그러나 그렇게 잘 표현했다고 해도 실제 디자인에서 키가 아닌 Attribute 에서 시작하는 함수적 종속성이 있을 수 있기에 정규화로 분해 과정을 거친다

### 역정규화(Denormalization)

- 정규화를 했다고 해서 무조건 좋은 것만은 아니다
- 정규화를 거쳐 분해된 테이블을 조회할 때, 분해되기 전 속성들을 볼 필요가 있다면 JOIN QUERY 가 필요해진다
  - 이렇게 되면 Overhead 가 발생
- 역정규화를 사용해야 하는 경우
  - 업데이트가 거의 발생하지 않고 조회 성능이 중요한 애플리케이션
  - 빠른 조회 성능을 제공해야 할 때
- 단, 역정규화를 하면 다음과 같은 문제가 발생한다
  - 중복 데이터 저장으로 저장 공간이 더 필요
  - Update 가 느려짐
  - 애플리케이션에서 이상 현상 등이 발생하지 않게 추가적인 코딩이 필요
- Alternative way : Materialized View
  - 필요한 속성들을 포함한 JOIN Query 에 대해 materialized view 를 생성
  - 테이블 형태로 저장하기에 denormalization 같은 성능을 제공
  - 추가적인 저장 공간이 필요하고 view 를 업데이트 하기 위한 비용도 필요
  - 하지만, 애플리케이션에서 이상 현상에 대한 에러 처리가 필요 없다.

## 4-2) 다른 디자인 이슈

### 정규화 과정을 통해 발견 못하는 디자인 이슈

- 새로운 데이터가 추가될 때마다 컬럼 추가가 필요한 경우
- 새로운 데이터가 추가될 때 테이블 추가가 필요한 경우
