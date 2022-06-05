# 1. Storage 와 파일 구조

## 1-1) DBMS Storage

- DBMS 는 데이터를 "Hard" Disk 에 저장 (영원히 지워지지 않게 만드는 것)
- Disk Access 가 DBMS 의 성능에 가장 중요한 문제
  - READ : Disk => Main Memory 로 데이터 전송
  - WRITE : Main Memory => Disk 로 데이터 전송
  - Higher cost than memory access
  - DBMS Storage 엔진은 Disk Access 를 어떻게 하면 줄일지에 중점을 두고 개발됨
- Main Memory 에 모두 저장할 수 없는 이유?
  - 메모리 가격이 비싸고
  - 메모리에 저장된 데이터는 volatile
- DBMS Storage 의 역할은 효과적인 Memory - Disk 데이터 전송을 위한 Buffer Management
- In Memory DB
  - 데이터베이스의 내용을 모두 메모리에 올리는 특성화된 DB
  - Disk Access 를 최소화해서 Disk Access Operation 에 드는 Cost 를 최소화
  - 모든 걸 다 In Memory 에 넣기 때문에 큰 메모리가 필요하고 Volatile 하고, Logging 은 똑같이 한다
  - 대신 좋은 성능

### Disk space Management

- DBMS 에서 가장 낮은 Layer 에서 Disk Space 를 관리
  - 데이터가 테이블을 저장할 때, 테이블을 각각의 작은 page로 만든다.
  - 그 후, page의 List 를 테이블 컨테이너를 구성한다.
  - page 단위로 메모리에 올라오고 내려온다고 보면 됨.
- 상위 Component 에서 다음과 같은 Request 를 요청한다
  - Allocate & De-Allocate a page
  - READ & WRITE a page
- Disk 는 하드웨어 차원에서 발생하는 seek delay, rotation delay 문제가 있다
  - 즉, Random Access 일 때 가장 성능이 안 좋다
  - 그래서 페이지들이 최대한 Sequential 하게 Allocate 하고 각 페이지들을 위에서 찾을 때 가장 근처에서, Sequential 하게 찾을 수 있도록 해야 한다.

### Buffer Management

- 페이지를 Read 하고 넣는 과정 중에 Main Memory 에 페이지를 저장하는 것이 Buffer
- Buffer 는 메모리 사이즈는 한정돼 있지만 데이터는 커서 LRU 에 의해 캐시 메커니즘으로 쓰지 않는 것은 없어지고 새로운 페이지로 채워진다. 버퍼 페이지 캐시 역할.
- 위에서 한 페이지를 읽고자 할 때 페이지에 대한 offset, 페이지의 ID 를 주면 Disk 의 Tablespace 에서 그 페이지를 읽는다.
- 버퍼는 각각의 페이지가 들어갈 수 있는 곳(프레임) 을 찾아서 로케이션 시킨다.
- page pin 은 이 페이지는 현재 쓰고 있다는 것을 의미. Buffer full 이 나도 이 페이지를 Reclaim, 삭제하지 말라고 마크하는 것. unpin 은 다 썼으니 공간이 부족하면 내려도 된다고 표시하는 것
- Buffer Management Free 프레임이 없을 때 unpin List 를 찾아서 없앤다

Page Buffering Proccess

- Buffer Pool 에서 Page 를 저장할 Frame 을 선택
  - Free Frame 이 있으면 선택
  - 없다면, Replacemkent policy (LRU) 에 의해서 오래된 Frame 을 Unpin 하고 이후에 Frame 을 결정
- Page 를 Disk 에서 읽고 Frame 에 저장
- Pin : pin_count ++, page 를 사용하는 사용자가 있음을 표시. -pin_count 가 0인 경우에만 replacement 대상
- Page 사용자는 사용이 끝나면 page 에 대한 Unpin 을 불러줘야 한다.
  - 만약, 업데이트 하는 상황이라면 Dirty Flag 를 세운다
  - 이 페이지는 Disk 에 내려갈 수 있고 내려갈 때, Tablespace 에 업데이트 해줘야 한다는 것을 의미
- 읽는 page list 가 예측되면 성능 향상을 위해서 prefetch 기능을 제공한다
  - 디스크의 페이지가 5개 할당 돼 있다고 하면,

## 1-2) Format in File

### Record Formats : Fixed Length

- 모든 필드의 타입과 사이즈에 대한 정보가 System catalog 에 저장
- 이 메타 데이터를 기반으로 해서 고정된 사이즈로 Record 를 저장
- 장점은 사이즈를 알고 있기 때문에 Record 점핑이 한 번에 가능하다. ACCESS 가 빠르다
  - 팔요한 Field Access 할 때 Pointer 연산으로 빠르게 주소를 반환할 수 있는 것
- 단점은 필요하지 않은 공간까지 만든다는 것

### Record Formats : Variable Length

- 대부분 이 포맷을 사용함. (자주 사용하는 Varchar 만 보더라도 사이즈가 유동적)
- 첫 번째 방법
  - Field Count 를 저장하고, 각각의 Field 에 Delimited 를 심어준다
- 두 번째 방법
  - Field Offset 을 배열로 앞에 저장한다
  - 앞 부분이 Pointer 의 Offset 이 되어 사이즈의 개념으로 저장된다
  - 사이즈가 저장되면 Value 가 커지기 때문에 사이즈가 아닌 Offset 으로 저장하는 것
- 두 번째 방법이 field 에 대한 direct access 가 가능하고, null 을 위한 저장 공간 효율성을 제공한다

# 2. 인덱스 (Index)

## 2-1) 인덱스란?

- SQL 을 배울 때, Search 성능을 높이거나 Unique 체크 - Key Search 를 위해서 인덱스를 생성한다
- 특정 기준에 맞는 rows 를 빠르게 찾기 위해 사용한다
- Trade-off 발생
  - Search 성능은 향상
  - 인덱스를 관리하는 비용 발생 (DML 성능 저하. Record 가 새롭게 Insert 할 때마다 Index Space 도 추가되기 떄문)
  - 인덱스를 저장하기 위한 Space 사용 증가
- Search Key

  - Index 검색을 위해 사용되는 Column 또는 Column 의 집합
  - Create Index 를 할 때, Column name 을 주어 Index 를 Search 하기 위함
  - 한 테이블에서 다른 Search key 를 가지는 다수의 Index 가 생성 가능하다
    - Primary key, Candidate Key 등등
    - 그러나 인덱스가 많아질수록 성능이 저하되므로 필요한 인덱스만 만들어야 한다

  ### 인덱스를 만들 때 고려할 사항

  - 인덱스를 언제 사용할 것인가?
    - 정확하게 Key 와 일치하는 row 를 찾을 때 사용?
    - Range Query 에 사용? (values between, >, <= 등)
    - 테이블에 모든 Row 를 조회할 때 사용? (굳이 Index 사용하시게요..?ㄷ)
  - 테이블이 얼마나 자주 업데이트 되는가?
    - DML 은 index 관리를 위한 UPDATE 가 발생
  - 인덱스의 key 가 super key 인가, primary key 인가
  - 하나의 key 로 여러 row 들이 가능한가? Unique or Not

## 2-2) 인덱스의 종류

### Tree(Ordered) Index

- Tree Index 는 Search Key 의 순서로 Index entry 가 정렬
- Range Query 나 Order by Operation 에 유리
- B+ Tree 가 주로 사용

### Hash Index

- Index Query 위치를 Hash Function 을 이용해서 Bucket 의 위치를 지정하여 저장
- Search 에 효율성 : O(1)
- DML 이 발생할 때 Index 관리에 유리하다

## 2-3) Index on SQL

- DBMS 는 다음의 경우에 Index 를 생성한다

  - Primary Key
  - Unique Constraint

- Create index statement in SQL

```sql
CREATE [UNIQUE] INDEX <index_name> [using<index type>] ON <table_name> (index_column_name, ...)

index_type : HASH, BTREE
```

# 3. B+ Tree Index

## 3-1) B+ Tree

- DBMS 에 Index 로 사용되는 Tree Structure
- 같은 레벨의 모든 Leaf 노드에 key 와 실제 data pointer 를 저장
- Components
  - Root Node (적어도 두 개의 Childeren 을 가질 때)
    - Search 를 시작하는 Node
  - Non-leaf Node
    - key 를 찾아가기 위한, key 의 range 를 나타내기 위함
  - Leaf Node
    - 실제 레코드에 대한 Rid, key 가 저장
- 차수 n(Order n) 는 노드 사이즈를 결정한다
  - Size of Node = ( n + 1 ) pointers + n key

## 3-2) Insert Into B+ Tree

### <1> Leaf Node 에 공간이 있는 경우

- 빈 공간에 새로운 key 를 넣을 수 있다면 간단하게 구현 가능
- 바로 키를 추가해서 Insert 를 완료

### <2> Leaf Overflow Case

- Sorting 기준에 맞춰 값이 추가 돼야 하는 Leaf-Node 가 가득 차 있는 경우
- (1) 새로운 Leaf-Node 를 만들어서 기존에 가득 차 있던 Node 를 쪼갠다
- (2) 새롭게 추가하려는 Key 를 기준으로 왼쪽으로 정렬해야 하는 것들을 새로 생성한 Leaf-Node 에 할당한다
- (3) 새롭게 추가하려는 Key 는 기존의 Leaf-Node 에 값을 넣는다
  - (\*) 혹은 새롭게 추가하려는 Key 와 그것 기준 오른쪽으로 정렬해야 하는 것들을 새로운 Leaf-Node 에 할당한다
- (4) Non-Leaf Node 에 새롭게 추가했던 Key 값을 넣어 Pointer 를 위한 Ragne 를 새롭게 정의한다

### <3> Non-Leaf Overflow Case

- Non-Leaf Overflow 현상이 발생한다는 건 Leaf Overflow가 발생해서 새롭게 Non-Leaf Node 에 키값을 주어야 하는데 가득 차 있다는 것을 의미

- (1) 새로운 Leaf-Node 를 만들어 할당하는 건 Leaf Overflow Case 와 동일
- (2) Sibling 위치에 Non-Leaf Node 를 새롭게 생성하고 Overflow 된 Non-Leaf Node 중 하나의 key 값을 새롭게 생성한 Node 에 옮긴다 (효율에 근거)
- (3) 새롭게 추가하려는 Key 는 해당 Non-Leaf Node 의 부모 Node 에 올라가서 새롭게 할당한다
  - (3-1) 만약, 그 부모의 Node 마저 Overflow 현상이 발생하면 위의 과정들을 반복한다
- (4) 이후 새롭게 만들거나 쪼개진 Non-Leaf Node 의 Pointer 를 새롭게 지정한다

### <4> New Root Case

- Non-Leaf 와 비슷하게 올라간다.

## 3-3) Delete from B+ Tree
### <1> Leaf Node
- 불필요한 empty node 를 만들지 않기 위해 적어도 다음의 개수의 Pointer 를 갖게 된다
- Non-Leaf : (n + 1) / 2 이하
- Leaf : (n + 1) / 2 이상 
- 만약에 삭제한 후 Leaf Node 에서 포인터의 개수가 위의 개수보다 줄어든다고 한다면 
  - 아예 해당 Leaf Node 를 지우고 Sibling Node 로 남아있는 key 값을 넘기거나
  - Sibling Node 의 포인터 개수가 여유 있는 경우 해당 Leaf Node 를 남기고 Sibling Node 에 있는 키값을 넘긴다
### <2> Redistribute Key
- key 의 조정이 이뤄지는 경우
- 위에서 처럼 key 값이 넘어가고 그럴 때마다 Non Leaf 의 키값이 계속 조정된다

### <3> Non-Leaf Coalesce
- Redistribute Key 가 일어나면서 Non Leaf Node 도 포인터 개수 제약에 위배되는 경우가 발생할 수 있다
- 그런 경우에는 Leaf Node 처럼 키값을 옮긴다
- 하다 보면 Root Node 가 의미 없어지는 경우가 발생해서 새로운 Root Node 를 만들기도 한다

