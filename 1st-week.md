# DB

## 트랜잭션이란?
- 트랜잭션이란 데이터베이스의 상태를 변화시키는 하나으 논리적인 작업 단위를 구성하는 연산들의 집합이다

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
- 데이터 무결성 보장
- 예기치 못한 오류가 발생하더라도 데이터베이스의 일관성을 유지할 수 있다

## 데이터 무결성(Integrity)
- 데이터가 정확하고 완전함
- 관계형 데이터베이스의 가장 큰 목표는 데이터 무결성을 높이는 것
- 무결성의 종류
  - 엔티티 무결성
    - 모든 인스턴스는 고유한 값이거나 null값을 가지면 안됨
  - 참조 무결성
    - FK는 참조되는 엔티티의 PK와 일치하거나 null값이어야 함
  - 도메인 무결성
    - 속성 값은 해당 속성에 정의된 도메인에 속한 값이어야 함
  - 비즈니스 무결성
    - 기업에서 요구하는 데이터 처리 규칙을 따라야 함

## 데이터 일관성/정합성(Consistency)
- 데이터들이 값이 서로 일치함  
- 정규화가 안되서 Anomaly(이상현상)이 발생하면 중복 데이터가 발생하고 결과적으로 정합성이 깨지게 됨  

## 트랜잭션의 상태
<img width="541" alt="스크린샷 2022-02-11 오후 12 15 08" src="https://user-images.githubusercontent.com/66231761/153532419-13438d9d-810e-45c1-8a75-bc0e6e717358.png">
- commit
  - 현재까지의 데이터 변경사항을 영구적으로 적용하고 트랜잭션을 종료하는 명령
- rollback
  - 현재 트랜잭션의 보류중인 모든 데이터 변경사항을 폐기하고 직전 커밋 직후 단계로 되돌아가는 명령
- partially Committed
  - 트랜잭션 마지막 연산까지 실행되고 Commit 연산 실행 직전 상태

## Lock
- Lock이란 트랜잭션 처리의 순차성을 보장하기 위한 방법이다
- Lock의 종류
  - 공유(Shared) lock
    - 데이터를 읽을 때 사용되는 lock
    - 공유 lock끼리는 동시 접근이 가능
    - 공유 lock이 설정된 데이터에 베타 lock은 사용불가
  - 베타(Exclusive) lock
    - 데이터 변경시 사용되고 트랜잭션 완료까지 유지되는 lock
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
- lock간 경합이 발생해서 특정 Transaction이 작업을 진행하지 못하고 멈춘 상태
- (베타 & 베타) or (베타 & 공유) 끼리 발생
- (공유 & 공유)는 발생하지 않음
- 블로킹을 해소하기 위해선 commit이나 rollback으로 트랜잭션을 종료해야 한다
- 블로킹은 성능에 좋지 않은 영향을 주므로 블로킹을 최소화해야 함

## 교착상태(DeadLock)
- 두 트랜잭션이 각각 lock을 설정하고 서로의 리소스를 얻고자할 때(lock이 풀리길 기다림) 양쪽 트랜잭션 모두 영원히 처리되지 않게 되는 상태

<img width="420" alt="스크린샷 2022-02-11 오후 12 53 22" src="https://user-images.githubusercontent.com/66231761/153535150-bdae3e93-82ac-47c7-9af0-0d317bc5e032.png">
- Deadlock 발생시 둘 중 한 트랜잭션에 에러를 발생시킴으로써 문제를 해결한다
- 교착상태가 발생할 가능성을 줄이기 위해 수정과 조회 연산을 동시에 수행하는 로직을 지향한다
- 위 예시에선 update 연산이 완료되면 commit을 호출해서 테이블 접근 교차가 일어나지 않도록 한다

## 성능과 무결성 사이의 트레이드오프
- locking으로 동시에 수행되는 트랜잭션들을 순서대로 처리할 수 있지만 blocking이 자주 발생해서 성능이 떨어진다
- 반대로 locking 빈도를 줄이면 성능은 좋아지겠지만 동시성 에러로 인해 데이터베이스의 무결성과 정합성을 보장할 수 없다
- 이러한 트레이드오프 상황에서 최대한 효율적인 locking 방법을 도입해야한다

## 트랜잭션 격리 수준(Transaction Isolation Level)
- 트랜잭션 격리 수준이라 트랜잭션끼리 얼마나 고립되어있는지 잠금수준으로 나타낸 것이다
- 레벨이 높아질수록 트랜잭션 간의 고립도가 높아지고 성능이 떨어진다

1. Read Uncommitted(level 0)
<img width="550" alt="스크린샷 2022-02-11 오후 7 43 45" src="https://user-images.githubusercontent.com/66231761/153577966-59787116-c602-45ca-a2b0-d4eceb0c209f.png">
- 트랜잭션 처리중에 아직 커밋되지 않은 데이터를 다른 트랜잭션이 읽는 것을 허용한다
- Dirty Read, Non-Repeatable Read, Phantom Read 현상이 발생한다
  - Dirty Read
    - 트랜잭션A가 수정중인데 외부에서 트랜잭션B가 A의 데이터를 조회하는 것
  - 
- 데이터 정합성을 보장하지 못해 RDBMS 표준에서는 격리 수준으로 인정하지 않는다

2. Read Committed(level 1)
<img width="550" alt="스크린샷 2022-02-11 오후 7 48 17" src="https://user-images.githubusercontent.com/66231761/153578517-e7fa91dd-cc2e-413b-bfd7-e6055dcff168.png">
- 실제 테이블 값을 가져오는 것이 아니라 Undo 영역에 백업된 레코드(커밋되서 더이상 변경되지 않을 값)에서 값을 가져온다
- 실제 온라인 서비스와 표준 RDB에서 기본적으로 사용되고 있는 격리 수준
- Dirty Read를 회피할 수 있다
- Non-Repeatable Read, Phantom Read는 여전히 발생
  - Non-Reapeatable Read
    - 하나의 트랜잭션이 같은 값을 여러번 조회할 때 다른 값이 검색되는 현상

3. Repeatable Read(level 2)
<img width="550" alt="스크린샷 2022-02-11 오후 7 57 15" src="https://user-images.githubusercontent.com/66231761/153579952-1e86e543-7c45-4bfd-8f92-dbe8b9ae8835.png">
- 트랜잭션마다 ID를 부여하여 트랜잭션 ID보다 작은 트랜잭션 번호에서 변경한 것만 읽게 된다
- 변경되기 전 레코드는 Undo 공간에 백업해두고 실제 값을 변경한다
- Non-Repeatable Read는 회피할 수 있지만 Update 부정합과 Phantom Read가 발생할 수 있다
- 트랜잭션이 길 경우 백업 레코드가 많아지는 단점이 있지만 그럴 경우가 거의 없어서 level1과 성능차이가 크게 나지 않음

4. Serializable(level 3)
- 선행 트랜잭션에 공유 Lock을 걸어 다른 트랜잭션에서 insert, update, delete 작업을 못하게 막는다
- 동시성이 저하되지만 성능 저하가 일어남

- 인덱스
  - B-Tree B*-Tree
  - 클러스터 인덱스 논클러스터 인덱스
- 1~3 정규화 샘플 예제까지 포함
  - 왜 정규화를 할까?

## OS

- Process vs Thread 의차이
    - 언제 무엇을 쓰는 것이 좋을까?
- Multi Process vs Multi Thread
- Heap 영역 Stack 영역 차이
    - stack은 정적인 처리, 스택 방식으로 마지막 요청부터 순차적으로 처리
    - heap은 동적인 처리
- Lace Condition 경합 상태
    - Lace Condition 또는 데드락을 회피 할수 있는 방법
- 동시성이슈
    - 하나의 어플리케이션내에서 (스레드끼리 동시성 이슈) 처리방법
    - 서로다른 어플리케이션에서 (프로세스 끼리 동시성 이슈) 처리 방법
    - 같은 DB를 바라볼 때 일어나는 이슈
- 인프런 slow query 인덱스로 인한 장애

# 네이버 메인페이지 트래픽 분산 방법

# References
- https://spidyweb.tistory.com/164
- https://github.com/WeareSoft/tech-interview
- https://sabarada.tistory.com/121
- https://velog.io/@guswns3371/데이터베이스-트랜잭션-격리수준
