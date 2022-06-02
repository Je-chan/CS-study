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
