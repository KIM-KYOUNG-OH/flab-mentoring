# DB

## 트랜잭션이란?
- 트랜잭션이란 데이터베이스를 조작하는 작업을 수행하는데 필요한 연산들의 집합이다

## 트랜잭션의 4가지 속성
- Atomicity(원자성)
  - 트랜잭션이 데이터베이스에 모두 반영되던가, 아니면 전혀 반영되지 않아야 한다
- Consistency(일관성)
  - 트랜잭션 완료 후에도 데이터베이스가 일관된 상태로 유지되어야 한다
- Isolation(고립성)
  - 둘 이상의 트랜잭션이 동시에 실행되고 있는 경우, 어떤 하나의 트랜잭션이라도 트랜잭션 연산에 끼어들 수 없다는 것
- Durability(지속성)
  - 트랜잭션이 성공적으로 완료된 경우, 결과는 영구적으로 반영되어야 한다는 것

## 트랜잭션을 왜 사용할까?
- 트랜잭션은 데이터베이스를 안전하고 일관성 있게 사용하기 위해 사용한다
- 이론적으로 위의 네 가지(ACID) 속성을 보장하기 위해 사용한다
- 트랜잭션에 속한 여러 쿼리 중 일부만 처리되는 경우를 막고 전부 실행되도록 한다(원자성)
- 예기치 못한 오류가 발생해서 일부 쿼리가 실패하더라도 직전 커밋 직후로 롤백해서 데이터베이스의 일관성을 유지할 수 있다(일관성)
- 송금을 예를 들면, 한 계좌에 송금과 입금 확인이 동시에 이루어지면 잘못된 결과가 보여질 수 있다. 트랜잭션이 동작 중에는 다른 트랜잭션의 간섭을 막기 때문에 이러한 문제를 사전에 차단할 있다(고립성)
- 커밋으로 변경 내용을 데이터베이스에 영구히 반영하고 시스템을 껏다 켜도 상태가 유지됨(지속성)

## 데이터 무결성(Integrity)
- 데이터가 정확하고 완전함
- 데이터들이 DBMS가 정해놓은 규칙과 개발자가 의도한대로 존재해야한다
  - 개체 무결성(PK 개념), 참조 무결성(FK 개념), 도메이 무결성(Column 개념)....
  - ex) 가격에 날짜가 들어간다거나...

## 데이터 일관성/정합성(Consistency)
- 데이터들이 중복/분산 되더라도 값이 모순이 없고 서로 일치함  
- 정규화가 안되서 Anomaly(이상현상)이 발생하면 중복 데이터가 발생하고 결과적으로 정합성이 깨지는 여지를 주게 됨  

## 트랜잭션의 상태
<img width="541" alt="스크린샷 2022-02-11 오후 12 15 08" src="https://user-images.githubusercontent.com/66231761/153532419-13438d9d-810e-45c1-8a75-bc0e6e717358.png">

- commit
  - 현재까지의 데이터 변경사항을 영구적으로 적용하고 트랜잭션을 종료하는 명령
- rollback
  - 현재 트랜잭션의 보류중인 모든 데이터 변경사항을 폐기하고 직전 커밋 직후 단계로 되돌아가는 명령
- partially Committed
  - 트랜잭션 마지막 연산까지 실행되고 Commit 연산 실행 직전 상태

## Lock
- Lock이란 동시에 실행되는 트랜잭션을 순차적으로 실행시키는 방법이다(동시성 제어)
- Lock의 종류
  - 공유(Shared) lock(S Lock)
    - 데이터를 읽을 때 사용되는 lock
    - 공유 lock 중에는 다른 공유 lock 요청은 허용하지만 베타 lock 요청은 배제함
  - 베타(Exclusive) lock(X Lock)
    - 데이터를 읽고 쓰기를 할 때 쓰는 lock
    - 베타 lock이 해제될 때까지 다른 트랜잭션은 해당 리소스에 접근할 수 없다

## Lock 설정 범위(Level)
- database lock
  - 전체 데이터베이스 lock
  - db 소프트웨어 버전 업데이트시 사용
- table lock
  -  테이블 기준으로 lock
  -  테이블 전체(모든 행)에 영향을 주는 변경시 사용
  -  DDL(CREATE, ALTER, DROP)과 함께 사용
- row lock
  - 행을 기준으로 lock
  - DML(INSERT, SELECT, UPDATE, DELETE)과 함께 사용

## 블로킹(Blocking)
- 동일한 데이터를 동시에 변경하려할 때 선행 트랜잭션이 나중 트랜잭션의 접근을 막는 행위
- (베타 & 베타) or (베타 & 공유) 끼리 발생
- (공유 & 공유)는 발생하지 않음
- 블로킹을 해소하기 위해선 선행 트랜잭션이 끝나길 기다리거나 commit/rollback으로 트랜잭션을 수동 종료해야 한다
- 블로킹은 성능에 좋지 않은 영향을 주므로 블로킹을 최소화해야 함

## 교착상태(DeadLock)
- 두 트랜잭션이 각각 lock으로 데이터를 점유하고 서로의 데이터를 얻고자 lock이 풀리길 기다릴 때 무한대기에 빠지는 상황

<img width="420" alt="스크린샷 2022-02-11 오후 12 53 22" src="https://user-images.githubusercontent.com/66231761/153535150-bdae3e93-82ac-47c7-9af0-0d317bc5e032.png">

## DeadLock 회피
- lock_timeout을 설정해서 일정 시간이 지나면 DeadLock이 발생했다고 판단해, 트랜잭션이 알아서 죽도록 함
- 교착상태를 피하기 위해 Row-Level Lock 과 READ_COMMITED 격리 수준을 사용한다
- 테이블 여러 개를 접근하는 경우, 같은 순서로 접근하도록 함
- index로 table 단위보다 row 단위로 lock 범위를 최소화

## 트랜잭션 격리 수준(Transaction Isolation Level)
- 트랜잭션 격리 수준이란 트랜잭션끼리 얼마나 고립되어있는지 잠금수준으로 나타낸 것이다
- 레벨이 높아질수록 트랜잭션 간의 고립도가 높아지고 동시성이 낮아진다

1. Read Uncommitted(level 0)
<img width="550" alt="스크린샷 2022-02-11 오후 7 43 45" src="https://user-images.githubusercontent.com/66231761/153577966-59787116-c602-45ca-a2b0-d4eceb0c209f.png">

- 트랜잭션 처리중에 아직 커밋되지 않은 데이터를 다른 트랜잭션이 읽는 것을 허용한다
- Dirty Read 발생
  - 트랜잭션A가 수정중인데 외부에서 트랜잭션B가 A의 데이터를 조회하는 것
- 데이터 정합성을 보장하지 못해 RDBMS 표준에서는 격리 수준으로 인정하지 않는다

2. Read Committed(level 1)
<img width="550" alt="스크린샷 2022-02-11 오후 7 48 17" src="https://user-images.githubusercontent.com/66231761/153578517-e7fa91dd-cc2e-413b-bfd7-e6055dcff168.png">

- 트랜잭션이 동시에 실행되며 실제 테이블 값을 가져오는 것이 아니라 Undo 영역에 백업된 레코드에서 값을 가져온다
- 실제 온라인 서비스와 표준 RDB에서 Default로 사용되고 있는 격리 수준
- Non-Reapeatable Read 발생
  - 하나의 트랜잭션이 같은 값을 여러번 조회할 때 항상 같은 결과값이 나오지 않는 현상

3. Repeatable Read(level 2)
<img width="550" alt="스크린샷 2022-02-11 오후 7 57 15" src="https://user-images.githubusercontent.com/66231761/153579952-1e86e543-7c45-4bfd-8f92-dbe8b9ae8835.png">

- 트랜잭션마다 ID를 부여하여 트랜잭션 ID보다 작은 트랜잭션 번호에서 변경한 것만 읽게 된다
- 변경되기 전 레코드는 Undo 공간에 백업해두고 실제 값을 변경한다
- Non-Repeatable Read는 회피할 수 있지만 Update 부정합과 Phantom Read가 발생할 수 있다
- 트랜잭션이 길 경우 백업 레코드가 많아지는 단점이 있지만 그럴 경우가 거의 없어서 level1과 성능 차이가 크게 나지 않음

4. Serializable(level 3)
- 선행 트랜잭션에 공유 Lock을 걸어 다른 트랜잭션에서 insert, update, delete 작업을 못하게 막는다
- 동시성과 성능이 저하됨

## 인덱스란
- 인덱스는 별도의 저장공간을 활용해서 테이블에 대한 검색 속도를 높여주는 자료구조이다
- 인덱스는 key-field 형태의 값만 갖고 있어서 테이블의 10% 정도로 가볍다

## 왜 인덱스를 사용할까?
- 테이블 크기가 커질수록 탐색 속도가 느려지는데 인덱스를 사용하면 검색 성능을 높일 수 있다
- SELECT절 뿐만 아니라 UPDATE, DELETE 시에도 빠르게 원하는 값을 찾을 수 있다

## 인덱스의 장단점
- 장점
  - 테이블 조회 속도 향상
- 단점
  - 인덱스를 저장할 추가적인 디스크 공간이 필요
  - 인덱스를 항상 정렬되고 최신 상태(사용하지 않는 인덱스는 삭제)로 관리하기 위해 오버헤드 발생
  - CREATE와 UPDATE, DELETE가 빈번한 테이블에는 오히려 인덱스가 비대해져서 성능이 오히려 저하될 수 있다
    - UPDATE, DELETE시 인덱스의 데이터를 삭제하는게 아니라 '사용하지 않음'으로 기록이 유지되기 때문

## 해시 테이블
  - 시간 복잡도 O(1)로 매우 빠름
  - 해시 테이블은 값이 조금만 바뀌어도 완전히 다른 해시값을 생성하는데, 등호(=) 연산에만 특화되었고 DB의 부등호 연산(<, >)시 적합하지 않다

## B-Tree
  - 이진 트리를 확장해 하나의 노드가 가질 수 있는 자식 노드의 최대 숫자가 2보다 큰 트리구조
  - 루트 노드의 자료수가 M이면 M차 B-Tree
  - 각 노드는 최소 M/2개의 데이터를 가지고 각 노드는 (해당 노드가 가진 데이터의 개수 + 1)개의 자식 노드를 가진다
  - 각 노드의 자료는 정렬된 상태여야 함  
<img width="477" alt="스크린샷 2022-02-14 오후 12 07 31" src="https://user-images.githubusercontent.com/66231761/153793535-98593797-3002-4f2e-8397-5397f747e910.png">

# B-tree를 왜 쓸까?
- 트리의 높이를 줄이기 위해 쓴다. 트리의 높이가 높아질수록 탐색 시간길어진다.
  - 이진 트리의 최악의 탐색 시간은 O(N)인데 B-tree는 트리의 높이가 상대적으로 낮아서 균일한 O(logN)의 시간복잡도를 가짐
- 항상 정렬된 상태이기에 부등호 연산에 유리하다

## B+tree
<img width="679" alt="스크린샷 2022-02-14 오후 12 37 37" src="https://user-images.githubusercontent.com/66231761/153796151-bf2f890d-826c-404c-b326-feace132e309.png">  

- B-tree와 LinkedList로 구현된 자료구조
- index 역할의 not leaf node(비단말 노드)와 linkedList로 순차적으로 연결된 leaf node로 구성됨
- 인덱스 영역의 key값은 leaf node의 key 값을 찾는데 용이함 

## B+tree를 왜 쓸까?
- 모든 노드에 key와 data를 동시에 가지는 B-tree와 달리 B+tree는 not leaf node에는 key값만 존재하고 leaf node에 모든 key와 data가 모여있다
- leaf node가 linkedList의 구조를 가지므로 범위 탐색에 유리하다

## 클러스터 인덱스

- 테이블을 특정 컬럼을 기준으로 물리적으로 정렬하는 방식으로 구현한 인덱스
- 테이블 하나당 하나의 클러스터 인덱스만 존재할 수 있다
- 별도의 공간을 필요로하지 않음
- PK를 생성하면 자동으로 클러스터 인덱스를 생성한다

## 어떤 경우에 클러스터 인덱스를 사용할까

- 항상 정렬되있으므로 범위 검색에 유리하다
- 클러스터 인덱스는 검색 속도 향상 뿐만 아니라 데이터 삽입시 많은 비용이 소모되는 단점이 존재한다
- 1부터 만 개의 데이터가 있는 테이블에 id가 2인 데이터를 인서트할 때 모든 컬럼을 한 칸씩 이동시켜줘야 한다. 이는 많은 비용을 요구하며 PK를 어떤 컬럼으로 선택하냐에 따라 DB 성능이 좌우된다

## 논클러스터 인덱스

- 별도의 공간에 index 테이블을 따로 두는 방식의 index
- 순서대로 정렬되있지 않다
- 한 테이블에 여러개 생성 가능하다
- JOIN, WHERE, ORDER BY 절에서 사용된 비 기본키 컬럼 위에 만들어진다

## 정규화

- 릴레이션을 분해해서, 이상 현상이 발생하지 않는 올바른 릴레이션으로 만들어나가는 과정

## 왜 정규화를 해야할까?

- 정규화는 데이터의 중복을 줄이기 위해서 사용한다
- 데이터 중복은 삽입, 갱신, 삭제시 이상 현상을 유발하고 이는 데이터베이스 정합성에 문제를 야기한다
- 데이터 중복은 디스크 공간의 낭비를 유발한다

## 제1 정규형(1NF)

- 릴레이션에 속한 모든 속성의 도메인이 원자값(atomic value)으로만 구성되어 있으면 제1 정규형에 속한다
- 원자값이라는 말은 모호한 표현이기 때문에 다음과 같이 바꾸는게 맞다
→ 모든 열과 행의 교차하는 지점에는 한개의 값만 가져야 한다

![스크린샷 2022-02-14 오후 9 51 51](https://user-images.githubusercontent.com/66231761/153867622-87a43ea7-840d-41b0-a3cc-305ca372407c.png)  

## 제2 정규형(2NF)

- 부분 함수 종속을 제거한 상태  

![스크린샷 2022-02-14 오후 9 52 22](https://user-images.githubusercontent.com/66231761/153867691-98415eb1-cd7c-418d-a1b6-2a3ad339cca7.png)  

## 제3 정규형(3NF)

- 이행적 함수 종속을 제거한 상태

![스크린샷 2022-02-14 오후 9 52 46](https://user-images.githubusercontent.com/66231761/153867774-9a1c745c-531d-4b23-a883-105d8d032de1.png)  

# OS

## 프로세스

- 메모리에 올라와서 실행중인 프로그램
- 프로세스 하나당 독립된 code, data, stack, heap 메모리 영역을 할당받음

## 스레드

- 프로세스의 실행 흐름을 쪼갠 단위
- 한 프로세스내에서 stack 영역만 따로 할당받고 다른 code, data, heap 영역은 공유함

## 멀티 프로세스

- 두 개 이상의 프로세스가 병렬로 작업을 처리하는 것
- 프로세스 하나가 죽더라도 다른 프로세스에 영향을 주지 않아서 안정성이 높지만 메모리 공간을 많이 차지함

## 멀티 스레드

- 둘 이상의 스레드로 동시에 작업을 처리하는 것
- 프로세스내 자원을 공유하므로 메모리 낭비를 줄이고, 응답시간이 단축됨
- 스레드 하나에서 오류가 나면 프로세스 전체에 영향을 줌

## stack 영역

- 함수의 호출과 관련된 지역 변수와 매개변수가 저장되는 영역
- 가장 마지막으로 호출된 함수부터 순차적으로 처리
- 정적 메모리 할당

## heap 영역

- 동적 메모리 할당
- 수시로 메모리 관리 필요

- Lace Condition 경합 상태
    - Lace Condition 또는 데드락을 회피 할수 있는 방법
- 동시성이슈
    - 하나의 어플리케이션내에서 (스레드끼리 동시성 이슈) 처리방법
    - 서로다른 어플리케이션에서 (프로세스 끼리 동시성 이슈) 처리 방법
    - 같은 DB를 바라볼 때 일어나는 이슈

# References
- https://spidyweb.tistory.com/164
- https://github.com/WeareSoft/tech-interview
- https://sabarada.tistory.com/121
- https://velog.io/@guswns3371/데이터베이스-트랜잭션-격리수준
- https://medium.com/humanscape-tech/데이터베이스-deadlock-해결-후기-ad45eb6f6ad8
- https://ko.wikipedia.org/wiki/인덱스_(데이터베이스)
- https://wlswoo.tistory.com/62
- https://sdesigner.tistory.com/79
