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
      
      
      