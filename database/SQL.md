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

### 2-5) DROP TABLE Syntax
```sql
DROP TABLE <table_name>

TRUNCATE TABLE <table_name>
```
- DROP 은 테이블을 모두 삭제(카탈로그까지 삭제)
- TRUNCATE 은 데이터들을 삭제하고 카탈로그는 남겨둔다 
        
        

# 3. SELECT, 데이터 조회
## 3-1) SELECT Syntax
### <1> SELECT 기본 
```sql
SELECT [DISTINCT] {* | <column_name>, ...} FROM <table list>
```

### <2> SELECT 산술식
- SELECT Clause 는 산술식을 포함할 수 있다
```sql
SELECT ID, NAME, SALARY / 12 FROM EMPLOYEE
```

## 3-2) SELECT 를 도와주는 CLAUSE, 데이터
### <1> WHERE
- 테이블에서 조건에 맞는 데이터만 검색하기 위해 WHERE 를 사용
```sql
SELECT [DISTINCT] {* | <column_name>, ...} FROM <table list> [WHERE Condtition]
```

- 문자열 컬럼인 경우 부분적인 매칭 조건을 만들 수 있다
  - % : 0개 이상의 문자가 스킵
  - _ : 1개의 문자를 스킵
```sql
# 리베리, 리베로, 리베디히 가능
SELECT * FROM EMPLOYEE WHERE NAME LIKE '리베%'
# 리베리, 리베로 가능
SELECT * FROM EMPLOYEE WHERE NAME LIKE '리베_'
```

### <2> NULL 
- null 은 empty 가 아닌 unknown 이다
  - null 값과 산술 연산 및 비교 연산 결과는 null
  - 5 + null returns null
  - null > 5 returns null ...

- null 과 논리 연산
  - OR : (null OR true) = true / (null OR flase) = null / (null OR null) = null
  - AND : (null AND true) = null / (null AND false) = false / (null and null) = null
  - NOT : (NOT null) = null

- IS NULL : null 값을 가진 row 를 찾기 위한 조건식
  - SELECT NAME FROM CUSTOMER WHERE AGE IS NULL

### <3> ORDER BY
- SELECT 의 검색 결과를 정렬하기 위해 ORDER BY 키워드를 사용
  - ASC 는 오름차순 정렬, DESC 는 내림차순 정렬
```sql
SELECT    [DISTINCT] {* | <column_name>, ...} 
FROM      <table list>
[WHERE    CONDITION]
[ORDER BY <column_name>, ... [ASC | DESC]] 
```
- TUPLE 의 순서는 보장되지 않는다.
  - 그러므로 INSERT 한 순서대로 나온다는 보장이 없다
  - 그래서 ORDER 를 사용하는 것
### <4> AGGREGATION FUNCTION
- 특정 컬럼의 값을 통계적으로 계산한 결과를 보여주는 SQL 내장 함수

함수의 종류
- COUNT : 컬럼 값의 개수 (동일한 값이라 하더라도 개수를 센다)
  - DISTINCT 를 사용하면 값이 동일하면 개수를 세지 않는다
  - COUNT(DISTINCT ID) 등
- MAX : 컬럼 값의 최댓값
- MIN : 컬럼 값의 최솟값
- SUM : 컬럼 값의 합계 
- AVG : 컬럼 값의 평균 
- 다른 COUNT, MAX, MIN 은 LOB 타입을 제외한 모든 타입에서 사용 가능
- SVG, AVG 는 숫자 데이터만 가능

NULL 핸들링
- MAX, MIN, SUM. AVG 의 경우 null 이 아닌 값으로만 계산
  - 컬럼 값이 모두 null 이거나 레코드가 없는 경우 null 을 리턴
- COUNT 는 null 이 아닌 값의 개수를 출력
  - 컬럼 값이 모두 null 이거나 레코드가 없는 경우 0 을 리턴

### <5> GROUP BY
- 테이블에서 특정 컬럼 값이 같은 rows 를 모아 그룹으로 만들고, 그룹별로 검색
```sql
SELECT    [DISTINCT] {* | <column_name>, ...}
FROM      <table list>
[WHERE    CONDITION]
[GROUP BY <column_name list> [HAVING CONDITION]]
[ORDER BY <column_name list> [ASC | DESC]]
```
- 그룹에 대한 조건을 추가하려면 HAVING 키워드를 사용한다
  - WHERE : 레코드를 GROUPING 하기 전에 조건을 검색
  - HAVING : 레코드를 GROUPING 한 다음에 그룹에 대한 조건 검색. 보통 연산된 결과에 대한 조건

그러므로 
- GROUP BY 로 명시한 컬럼은 SELECT 로 조회하는 것에 반드시 있어야 한다
- GROUP 을 나누는 건 AGGREGFATION 하기 위한 용도이므로 함수가 있어야 한다
- AGGREGATION 은 컬럼의 결과에 대한 통계를 내는 것이므로 

## 3-3) SET (관계 대수 => SQL)
### Union => UNION
```sql
SELECT A, B FROM R UNION SELECT A, B FROM S
```

### Difference => EXCEPT
```sql
SELECT A, B FROM R EXCEPT A, B From S
```

### Intersection => INTERSECT 
```sql
SELECT A, B FROM R INTERSECT A, B From S
```

## 3-4) JOIN
### Cartesian Product 
```sql
SELECT * FROM r, s
```

### Natural Join 
- DBMS 에서 지원 잘 안 해줌.
- 보통은 쎄타 조인을 한다
```sql
SELECT * FROM R Natural join S
```

### Theta Join (Inner Join)
- 많은 DBMS 에서 사용되는 Inner JOIN 문
- ON 이 Theta.
```sql
SELECT    R.A, R.B, R.C, R.D, S.E
FROM      R
JOIN      S
ON        R.B = S.B AND R.D = S.D
```

### OUTER JOIN (LEFT Join, OUTER Join, FULL outer join)
- FULL outer join 은 지원하는 DBMS 도 있어서 이건 그냥 UNION 사용하는 게 더 나을 듯하다


# 4. DML : 데이터 조작
## 4-1) INSERT
### 기본
```sql
INSERT <table_name> [(<column_name>, ...)] VALUES (value, ...)
```
- Optional 인 column_name 을 적지 않으면 전체 컬럼에 대한 Insert 가 된다
- 구체적으로 Column name 이 지정되면 뒤에서 Value 의 순서에 맞춰서 일대일로 매칭되어 값이 들어간다

### 특이 케이스
다른 테이블에서 질의 결과를 삽입하는 구문
```sql
INSERT INTO <table_name> [(<column_name>, ...)] <select clause>

#Eg
INSERT INTO STUDENT SELECT * FROM NEW_STUDENTS
```
- 이렇게 사용하고 싶다면 Insert 하는 컬럼 리스트와 SELECT 문의 컬럼 리스트가 일치해야 한다. 컬럼이 맞지 않으면 에러가 난다

## 4-2) DELETE
```sql
DELETE FROM <table_name> [WHERE CONDITION]

#Eg
DELETE FROM STUDENT # 모든 컬럼 삭제
DELETE FROM STUDENT WHERE DEPT_NAME = 'English'

DELETE FROM STUDENT 
WHERE       DEPT_NAME 
IN( 
    SELECT  DEPT_NAME 
    FROM    DEPARTMENT 
    WHERE   BUILDING = 'C'
  )
```
- 테이블에서 쿼리에 해당하는 ROW 를 지운다
- TRUNCATE 와 동일한 결과를 낳지만 내부적으로는 다른 로직이 돌아간다

## 4-3) UPDATE
```sql
UPDATE <table_name> SET <column_name> = value [WHERE CONDITION]

#Eg
UPDATE EMPLOYEE SET SALARY = SALARY * 1.07
UPDATE EMPLOYEE SET SALARY = SALARY * 1.07 WHERE HIRE_DATE < '2022.01.01'

UPDATE  EMPLOYEE 
SET     SALARY = SALARY * 1.07 
WHERE   DEPT_NAME 
IN(
    SELECT  *
    FROM    DEPARTMENT
    WHERE   LOCATION = "GYEONGGI"
  )

```
- 어떤 조건에 맞는 컬럼을 찾아서 Value 를 바꾸라는 것 
- SET 으로 해당 컬럼의 값을 value 로 바꾸라. 
- 만약 WHERE 조건문이 없다면 컬럼 전체 값을, 있다면 조건에 맞는 컬럼의 값만을 value 로 수정한다
