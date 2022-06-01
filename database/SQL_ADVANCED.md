# 1. Views
## 1-1) 기본 
### Views 개요
- External Level(External Schema) 를 위해서 사용되는 것
- 사용자가 보는 View Level
- 사용자에게 View 의 형태로 필요한 정보만 보여주고, 요구 사항에 따라 다른 View 를 정의한다
- 데이터베이스 하나에 여러 외부 스키마가 가능. 

### View 의 목적
- 어떤 경우에 모든 사용자가 전체 논리적 모델(실제 테이블)을 보는 건 문제가 될 수 있다
- 필요한 데이터만 특정 사용자들에게 유출할 필요가 있을 때 사용한다 (보안상의 문제)
  - 만약 Employ 라는 테이블에서 아이디, 이름, 부서는 조회할 수 있지만 Salary는 숨기고자 할 때
- 질의문 작성을 쉽게 만들어 준다
  - Group By, Aggregation Function 등을 미리 정의
- 데이터 종속성을 제거
  - 응용 프로그램은 뷰를 통해 접근해서 테이블 스키마 변화에 신경쓸 필요가 없다


### View 정의하기
```sql
CREATE VIEW <view_name> [(<column_name_list>) as <query expression>]
```
- 다른 view 를 이용한 query 로 view 생성 가능
- column list 가 생략된 경우, 쿼리의 결과로 나온 릴레이션의 컬럼 리스트로 View 컬럼이 지정된다
- 스키마의 변화에 신경 쓸 필요가 없다

```sql
CREATE  VIEW public_employee_information
AS
SELECT  ID, dept_name
FROM    emoloyee
```

### View 가 Query Expression 으로 대체되는 로직
- DO
  - query 절에서 View relation 을 찾는다
  - View relation 을 View 의 정의로 치환한다.
- While
  - 더이상 View 가 없을 때까지 계속 돈다
- 덕분에 긴 쿼리로 작성해야 할 것들이 축소된다

### View 에 대한 삽입, 수정, 삭제 연산도 가능
- 기본 테이블에 대한 연산으로 변경돼서 실행한다
```sql
INSERT INTO   public_employee_information VALUES ('10101', 'Green', 'Sales')
```
- 이렇게 하면 뒤에 나오는 Value 의 값들이 employee 테이블에 대한 Insert 로 변환된다
- 단 조건이 존재한다
  - 베이스 테이블이 하나인 경우 (join 인 경우는 불가능)
  - SELECT Clause 에 컬럼 이름만 있는 경우 (Aggregation 함수, DISTINCT 있으면 불가능)
  - Group By, Having 이 없는 경우 
    - Where 절은 됨 (Where 절은 무시돼서 있다 해도 사용할 수 있다) 

### Materialized View
- Query Expression 으로 나온 결과를 View 에 대한 Select 가 있을 때마다 매번 계산을 해서 질의문의 결과로 리턴하는데 그 SELECT 가 너무 복잡하다면 성능이 많이 느려진다
- 그럴 때, 쿼리 결과를 미리 계산하고 저장하여 View 에 대한 쿼리 성능을 높인다
- Materialized view maintenance
  - 다른 Base 테이블들에 수정이 있을 때 어떻게 할 것인가
  - View 에서 사용되는 베이스 테이블들이 업데이트가 있을 때, Materialized View 도 업데이트 해주는 방법
  - on-demand mode : Base가 바뀌면 이것도 바꿔줘 라고 명시하는 것
  - real-time mode : 트리거를 만들어서 베이스가 바뀔 때마다 동시에 업데이트. 제한적인 조건이 존재한다.

### Drop View
```sql
DROP VIEW <view_name>
```
      
      
      
# 2. 트랜젝션과 SQL
## 2-1) 트랜잭션(Transaction)
- LUW(Logical Units of Work) : 논리적인 작업의 단위
- 트랜잭션은 Read, Write, Delete, Update 등의 연산들로 구성되나 한 Transaction 단위로 일관성이 보장된다
- 흔히, ACID 라고 부른다

### Atomic(원자성)
- All OR Nothing
- Transaction 을 구성하는 연사들이 모두 실행 되거나 모두 실행되지 않거나
- 연산이 실패하면 이미 실행된 연산들도 다시 원상태로 되돌린다 

### Consistency(일관성)
- 트랜잭션이 실행된 후에도 일관성 있는 데이터베이스 상태로 유지하는 것
- 무결성 제약 조건으로 정의된 일관성 조건이 걸려 있는데 트랜잭션의 구성하는 연산 중 조건을 어기는 게 있다면 실패
### Isolation(격리성)
- 트랜잭션을 수행 시 다른 트랜잭션의 연산 작업이 끼어들지 못하도록 한다
- 또, 다른 트랜잭션들이 트랜잭션 안의 중간 연산 결과를 볼 수 없다
### Durability(지속성)  
- 성공적으로 수행된 트랜잭션은 영원히 반영돼야 한다
- 시스템에 장애가 발생했다 하더라도 성공적으로 수행된 트랜잭션의 결과는 데이터베이스에 반영되어 있어야 한다
- 전형적으로 트랜잭션은 업데이트에 대한 로그로 적고, 로그가 저장된 후에 트랜잭션이 Commit 으로 간주한다
- 장애 후, 로그 데이터를 갖고 데이터베이스를 재구성

## 2-2) Transaction Mode
### Autocommit mode (Default)
- Statement 가 시작할 때 transaction 이 내부적으로 시작
- 매 statement 가 끝날 때마다 commit 이 자동으로 실행된다
- Session Level 로 모드를 변경할 수 있다
  - SET AUTOCOMMIT = OFF
  - JDBC L connection.setAutoCommit(false)
- 다른 커넥션들과는 독립적으로 내가 만든 것에만 바뀐다

### Explicit Mode
- Auto commit 모드를 끈 후,
- START TRANSACTIONM (트랜잭션 시작)
- COMMIT (트랜 잭션의 commit 에서 변경 내용을 데이터 베이스에 저장)
- ROLLBACK (트랜잭션의 연산들로 변견된 내용을 취소)
```sql
START TRANSACTION
UPDATE accounts SET balance = balance - 5000 WHERE accId = "리베"
UPDATE accounts SET balance = balance + 5000 WHERE accId = "JE"
COMMIT
```

### Transaction Isolation Level
- SET TRANSACTION statement 로 transaction isolation level 을 바꿀 수 있다
- Isolation level
  - READ UNCOMMITTED
    - 트랜젝션이 처리중에 아직 Commit 되지 않은 데이터를 다른 트랜젝션이 읽는 걸 허용
    - Dirt Read, 데이터의 일관성을 유지할 수 없다
  - READ COMMITED (대부분)
    - Commit 이 된 데이터만 접근해서 읽을 수 있다
    - 문제점은 한 트랜젝션 안에서 같은 SELECT 가 여러 개 있을 때 먼저 시작한 SELECT 의 내용이 보이게 되어 다른 결과를 리턴할 수 있다
    - Non-Repeatble Read
  - REPETABLE READ (MySQL 에서 Default)
    - 트랜젝션 안에서의 같은 조회는 항상 동일한 결과가 리턴하는 것을 보장
    - Phantom Read
  - SERIALIZABLE
    - 완벽한 읽기 일관성
```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE
```
- 성능 향상을 위해 DBMS 는 Isolation level 을 조절하는 기능을 제공한다
- 레벨이 낮을수록 데이터 무결성이 유지되지만 비용이 높아지고 동시성이 떨어진다
- 여러 트랜젝션이 실행되고 있을 때, 원래의 방식은 Locking 을 걸어서 다른 트랜잭션의 내용을 못 보게 만드는데 Locking 을 건 만큼 성능, 동시성이 떨어지는 것
- 만약 개발자가 그 Isolation 을 확실하게 지킨다고 한다면 성능을 높일 수 있음

# 3. 무결성 제약 조건 (Integrity Constraint)
## 3-1) CREATE TABLE
### not null
- 컬럼 값으로 null 을 허용하지 않을 때 지정
- name char(10) NOT NULL : 고객 이름 컬럼으로 사이즈 10 이내, 필수 입력 사항이라는 뜻

### Unique
- 튜플에 유일성을 체크하는 대체 키를 지정
- null 을 허용

### primary key
- 테이블에서 튜플(row)을 찾는 기본 키
- Unique + not null
- 만약 같은 값이 이미 테이블에 존재하거나 null 을 입력하려고 하면 INSERT 실패

### Check(p) : predicate
- 특정 속성에 값의 도메인을 check 키워드를 사용하여 지정할 수 있다
- 즉, 도메인의 값을 단순 타입이 아니라 조건까지 추가하는 것


## 3-2) 참조 무결성 (Referential Integrity)
- 관계 데이터베이스 관계 모델에서 2개의 관련 있는 테이블 간의 일관성 (데이터 무결성)을 유지하는 것
- 참조 무결성을 정의하기 위해 Foreign key (외래키) 를 지정
- Foreign key 에 포함되는 컬럼은 참조하는 부모 테이블의 Primary key 또는 Candidate Key 여야 한다
```sql
FOREIGN KEY (<column_names>) REFERENCES <parent_table_name>
(<column_names>)
  [ON DELETE reference_option]
  [ON UPDATE reference_option]
```
- 위의 ON DELETE, ON UPDATE 는 parent 테이블에 각각 DELETE, UPDATE 가 일어날 떄 어떻게 할 것인지를 설정하는 것 (참조 무결성을 위한 것)

### 사용할 수 있는 reference_option
```sql
# 튜플을 삭제하지 못하게 만든 방법 (default)
ON DELETE(UPDATE) NO ACTION 

# 관련된 튜플을 함께 삭제하는 방법
ON DELETE(UPDATE) CASCADE

# 관련된 튜플의 외래키 값을 NULL 로 설정하는 방법
ON DELETE(UPDATE) SET NULL

# 관련된 튜플의 외래키 값을 default 값으로 설정하는 방법(default 가 없으면 에러)
ON DELETE(UPDATE) SET DEFAULT
```
