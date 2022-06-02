# 1. 데이터베이스 설계
- 사용자의 다양한 요구 사항을 고려하여 데이터베이스 생성
- 이미 구축된 데이터베이스는 구조를 변경하기 어려우므로 체계적인 설계 과정이 필요
- 대표적인 설계 방법
  - E-R 모델과 Relation 변환 규칙을 사용한 설계
  - 정규화를 이용한 설계

## 1-1) 데이터베이스 설계 단계 
### 요구사항 분석
- 실제 세계에서 어떤 요구사항의 시스템을 구축할 것인지에 대한 데이터베이스 용도 파악
- 요구 사항에 대해 어떤 데이터가 필요한지, 어떤 기능이 필요한지 분석
- 데이터베이스를 사용할 주요 사용자의 범위를 결정
- 사용자가 수행하는 업무를 분석
- 수집된 요구 사항에 대한 분석 결과를 요구 사항 명세서로 작성

### 개념적 설계(Conceptual Model)
- 요구 사항 분석 결과물을 개념적 데이터 모델을 이용해 개념적 구조로 표현
- DBMS 에 독립적인 개념적 스키마를 설계하는 과정

STEP 1. 요구 사항 분석 결과를 기반으로 중요한 개체를 추출
- 개체 : 저장할만한 가치가 있는 중요 데이터를 가진 사람이나 사물
  - 병원 운영에 필요한 사람 : 의사, 간호사, 환자
  - 병원 운영에 필요한 사물 : 병실, 수술실, 의료 장비 
- 개체 추출 방법
  - 요구 사항 문장에서 업무와 관련이 깊은 의미 있는 명사 찾기
  - 찾아낸 명사를 개체와 속성으로 분류하기

STEP 2. 개체 간의 관계를 결정하여 E-R 다이어그램 작성
- 관계 : 개체 간의 의미 있는 연관성
- 관계 추출 방법
  - 요구 사항 문장에서 개체 간의 연관성을 의미 있게 표현한 동사를 찾는다
  - 찾아낸 관계에 대해 매핑 Cardinality 와 참여 특성을 결정
    - 매핑 Cardinality : 1:1, 1:n, m:n
    - 참여 특성 : Total / Partial  
STEP 3. 요구 사항 명세서 바탕으로 E-R 다이어그램 표현

### 논리적 설계
- 관계 모델(Relational Model)로 개념적 모델을 논리적으로 표현
- E-R 다이어 그램으로 Relational Schema 
- 

### 물리적 설계
- DBMS 구현이 가능한 물리적인 구조를 설계
- 저장 레코드 타입 및 인덱스 타입 등을 설꼐
- Relational Schema 로 물리적 스키마를 만듦

# 2. E-R 다이어그램
## 2-1) E-R Model (Entity Relation Model)
- 요구 사항에서 얻어 낸 정보를 개체(Entity), 속성(Attribute), 관계성(Relation) 으로 기술하는 모델
- 표현 방법은 두 가지
  - Chen Notation
  - Crow's Foot Notation

## 2-2) 개체(Entity)
- 저장할 만한 가치가 있는 중요 데이터를 가진 사람이나 사물
- 개체들의 집합을 Entity Type 이라 한다
- Chen Notation 에서 네모로 표현
- Weak Entity
  - 포함된 속성만으로 고유하게 식별할 수 없는 개체를 뜻함
  - Weak Entity 는 본인을 소유하는 Entity 에 의존한다
  - 이런 경우 중첩 네모를 사용한다
  - 예를 들어, Order Item(주문 상품) 은 주문(Order) 에 종속적이다
  - Order 에서 어떤 아이템을 샀는지, 리스트가 나오기 떄문. Order 없이 Order Item 이 없다

## 2-3) 속성(Attribute)
- Attribute 는 이름을 포함한 원으로 표현된다
### Key Attribute
- 특정 Entity 를 식별할 수 있는 고유한 값을 가진 Attribute
- 이름에 밑줄을 그린다
### Multivalued Attribute
- 하나의 Attribute 가 여러 개의 값을 가질 수 있는 경우
- 중첩 원으로 표현
- 그러나 사실, 논리 단계에서 이런 경우는 없어야 한다.
### Derived Attribute
- 다른 Attribute 들로부터 계산되어 나온 Attribute
- 점선으로 된 원으로 표현

### Composite Attribute
- Attribute은 객체에도 링크가 될 수 있고 다른 Attribute에도 링크가 걸릴 수 있다
- Attribute을 가진 Attribute 라는 의미

## 2-4) 관계(Relation)
- Entity 간 어떤 상호작용을 하는지 표현하는 Notation
- Relationship 이름을 포함한 다이아몬드(마름모)로 표현
- Relationship 도 Attribute 을 가질 수 있다
- Weak (identifying) relationship
  - Weak Entity 와 parent-child 관계에서 상호작용을 표현

## 2-5) Cardinality (Degree of Relationship)
- 관계를 맺는 두 Entity 에 대해 한 개체가 얼마나 많은 다른 개체와 관련될 수 있는 지 표현
### 매핑 
- 1, N, M 으로 나타낸다
  - 일대일 (1:1)
  - 일대다 (1:N)
  - 다대일 (N:1)
  - 다대다 (M:N)

### 참여 특성
- Entity set 이 Relationship 에 전체로 참여하는지, 부분적으로 참여하는지 표현
  - Total Participation
    - 모든 Entity 가 Relationship 에 참여
    - Double line 으로 표현
  - Partial Participation
    - Entity Set 에서 일부분만 Relationship 에 참여
    - Single line 으로 표현 
    
# 3. 릴레이션 스키마 변환
## 3-1) 논리적 설계
- 관계 모델을 통해 개념적 모델을 논리적으로 표현
- 개념적 스키마를 논리적 스키마로 변경하는 과정
- E-R 다이어그램과 관계 데이터 모델의 차이점
  - E-R 모델은 Entity 와 Relationship 을 구분하지만 관계 데이터 모델은 모두 릴레이션으로 표현
  - E-R 모델은 Multivalued Attribute, Composite Attribute 를 표현하지만, 관계 데이터 모델은 그런 속성 허용하지 않음
  - E-R 모델의 Cardinality, Participation Constraint 도 관계 데이터 모델이서는 그에 맞는 변경이 필요

## 3-2) 릴레이션 스키마 변환 규칙
### 1. 모든 개체는 릴레이션으로 변환한다
  - Entity Name -> Relation Name
  - Entity Attribute -> Relation Attribute
  - Entity Key Attribute -> Relation Primary key
  - Compose Attribute (복합 속성) 이 있는 경우는 Compose Attribute 의 Attribute 를 Relation 의 Attribute 로 변경
    - 한 마디로 풀어서 쓰면 된다는 것

### 2. 다대다(n:m) Relationship(관계)는 릴레이션으로 변환한다
- Relationship Name -> Relation name
- Relationship Attribute -> Relation Attribute
- 관계에 참여하는 기본 키를 관계 릴레이션에 포함시키고 외래키로 지정한다
  - 같은 이름이 이미 존재하면 이름을 변경해야 한다

### 3. 일대다(1:n) Relationship(관계)는 외래키로 표현한다
- 1:n 관계에서 1측 Entity 릴레이션의 기본키를 n측 Entity 릴레이션에 포함시켜 외래키로 지정한다
- Relationship 의 Attribute 들도 n측 릴레이션에 포함시킨다
- Weak Entity 의 경우, 기본키를 Parent entity 의 기본키를 외래키로 받은 후, 그 키를 포함해서 기본키를 만든다

### 4. 일대일(1:1) 관계는 외래키(Foreign Key)로 표현한다
- 일대일(1:1) 관계도 일대다(1:n) 와 마찬가지로 릴레이션이 아닌 외래키로 표현한다
- 데이터 중복을 피하기 위해 참여 특성에 따라 다르게 처리한다
  - 4-1) 일반적인 일대일 관계는 외래키를 서로 주고 받을 것
    - 관계에 참여하는 개체 릴레이션들이 서로의 기본 키를 외래키로 지정
    - 관계의 속성들도 모두 개체 릴레이션에 포함
    - 단, 데이터 중복이 발생할 수 있으므로 관계(Relationship)를 릴레이션으로 바꾸지 않는 것을 추천한다 
  - 4-2) 일대일 관계에 필수적으로 참여하는 개체가 잇는 경우, 필수 참여 개체의 릴레이션만 외래키로 받을 것
    - 일대일 관계에 필수적으로 참여하는 개체가 있는 경우, 필수 참여 개체의 릴레이션만 외래키로 포함시킨
    - 관계의 속성들은 필수적으로 참여하는 개체의 릴레이션에 포함시킨다 (필수인 한 쪽만 가지면 되기 때문)
  - 4-3) 모든 개체가 일대일 관계에 필수적으로 참여하면 릴레이션을 하나로 합칠 것
    - 관계에 참여하는 개체 릴레이션들을 하나의 릴레이션으로 합친다
    - 관계 이름을 릴레이션 이름으로 사용, 참여하는 두 개체의 속성들을 다 포함시킨다
    - 두 개체 릴레이션의 키 속성을 조합하여 기본키로 만든다
    - 관계의 속성들은 필수적으로 참여하는 개체의 릴레이션에 포함시킨다

### 5. Multivalued Attribute(다중 값 속성) 은 릴레이션으로 변환한다
- 다중 값 속성과 그 속성을 가지고 있던 개체의 기본키를 외래키로 포함시킨다.
- 다중 값 속성으로 만들어진 새로운 릴레이션의 기본키는 다중값 속성과 외래키를 조합하여 지정한다.