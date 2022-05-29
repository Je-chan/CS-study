# 1. SQL 소개
- SEQUEL (Structured English QUEry Language)
  - 연구용 DBMS 인 SYSTEM R 을 위한 언어
- SQL (Structured Query Language) 
  - SEQUEL 에서 이름이 바뀐 것
  - ANSI/IO 가 표준 Standard SQL 을 지정

## 1-1) SQL 소개
- 대부분의 DBMS 는 SQL-92 표준의 대부분을 지원하고 추후 표준에 지정된 기능을 추가하여 지원, 고유의 기능을 제공
- SQL-2011 에 추가된 기능
  - Temporal Table
  - 데이터 변경 내용(Insert, Delete 등) 의 전체 기록을 유지
  - 지정된 시간에 어떤 데이터를 갖고 있었는지 시간 분석을 허용

## 1-2) SQL 분류
### DDL
- 테이블을 생성하고 변경, 제거하는 기능을 제공한다

### DML
- 테이블에 새 데이터를 삽입하거나, 테이블에 저장된 데이터를 수정, 삭제하는 기능을 제공

### SELECT QUERY(DML 에 포함되기도 함)
- 테이블 데이터를 조회하는 기능을 제공

### DCL
- 보안을 위해 데이터에 대한 접근 및 사용 권한을 조절하는 기능을 제공
- Role 을 만들거나, 사용자가 이 테이블을 볼 권한을 준다든지 하는 것
        
        
## 1-3) 관계 데이터 모델과 SQL 의 용어
- Relation = Tuple
- Tuple = Row
- Attribute = Column
- Relational Schema = Table Schema
        
# 2. DDL 데이터 정의
## 2-1) DDL : 데이터 정의
- 데이터베이스 구조를 정의하고 변경하는 기능을 제공하는 언어
- CREATE : 새로운 데이터베이스 오브젝트들을 생성
  - Schema, Table, View 등을 생성
- ALTER : CREATE 로 만들어져 존재하는 오브젝트의 정의를 변경
  - 기존의 테이블에 컬럼을 추가하거나 지우는 등
- DROP : 존재하는 오브젝트를 데이터베이스에서 삭제

## 2-2) 데이터 타입
- CHAR(n) : 길이가 n으로 고정인 문자열
- VARCHAR(n) : 최대 길이가 n인 가변 길이의 문자열
- BIGINT : 8바이트 정수
- INT / INTEGER : 4바이트 정수
- SMALLINT : 2바이트 정수
- NUMERIC(p, s) or DECIMAL(p, s) : 고정 소수점 실수
- FLOAT(n) : 길이가 n인 부동소수점 실수
- REAL : 부동 소수점 실수
- DATE : 연원일
- TIME : 시분초
- TIMESTAMP : DATE + TIME
- BLOB : Binary Large OBject (기가바이트 단위까지)
- CLOB : Charcter Large OBject (기가바이트 단위까지)

## 2-3) CREATE TABLE Syntax
```sql
CREATE TABLE <table name> {<column definitions> | <like_table_clause>}
    <column definitions> := (<data column definition>, ...)
      <data column definition> := <column name> <data type> [<default value> | <integrity constraint>]

    <default value> := DEFAULT <value>

    <integrity constraint> := {UNIQUE | <primary key> | <reference constraint> | <check constraint>} 
```


### Default Clause
```sql
<default value> := DEFAULT <value>
```
- 컬럼을 정의할 때 사용
- INSERT 문에 컬럼 값이 지정되지 않았을 때 null 값 대신 \<value> 사용
- grade INT DEFAULT 0
- 변수의 초기 값을 지정한다고 생각하면 된다

### Constraint Clause
```sql
<integrity constraint> := {UNIQUE | <primary key> | <reference
constraint> | <check constraint>}
```
- UNIQUE : 대체 키를 지정하고 유일성을 가지는 컬럼임을 지정
- PRIMARY KEY : ROW 를 식별할 수 있는 기본 키 (unique + not null)
- \<reference constraint> : 참조하는 테이블을 지정하는 외래키 설정. 
- \<check constraint> : 컬럼 값의 가능 domain 지정

### Like Clause
```sql
CREATE TABLE <table name> <like_table_clause>

<like_table_clause> := LIKE <table_name> [WITH DATA] 
```
- 테이블을 생성할 때, 기존의 테이블을 활용해서 만들 때 사용
- WITH DATA 까지 해준다면, 기존에 갖고 있던 데이터까지 모두 카피해서 가져온다
        
        
## 2-4) ALTER TABLE Syntax
- 새로운 컬럼 추가
```sql
ALTER TABLE <table_name> ADD <column_name> <column_type> [not null] [<default_value>]
```
- 기존 컬럼 삭제
```sql
ALTER TABLE <table_name> DROP COLUMN <column_name>
```

- 새로운 제약 조건 추가
```sql
ALTER TABLE <table_name> ADD CONSTRAINT <constraint_name> <constraint>
```

- 제약 조건 삭제
```sql
ALTER TABLE <table_name> DROP CONSTRAINT <constraint_name>
```







