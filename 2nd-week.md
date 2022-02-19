# Java 메모리 구조

<img width="500" src="https://user-images.githubusercontent.com/66231761/154805052-79e00bd0-3580-423a-86bd-6c8a9f21c08c.png">

- 자바 프로그램은 JVM 위에서 실행되며 OS로부터 메모리 영역을 할당받는다
- java의 철학은 OS에 의존하지 않고 어떠한 환경에서도 JVM이 설치된 PC 어디서든 바이트코드를 실행가능하도록 함
- 메서드 영역과 힙 영역은 모든 스레드가 공유한다
- stack, pc register, native method stack은 스레드마다 존재

# 메서드 영역

- 프로그램이 실행될 때 생성되고 클래스의 정보를 파싱해서 저장하는 영역

# 힙 영역

- 런타임에 생성되는 모든 Object 타입 인스턴스가 동적으로 저장되는 영역
- 더이상 사용되지 않는 객체는 gc에 의해 주기적으로 제거됨
- threadsafe 하지 않음
- OS로 부터 할당받는 메모리 크기가 유동적(동적 할당)
- 수시로 관리 해주지 않으면 단편화가 발생한다
- default size: 최소 16MB 최대 512MB(Java 8버전)

<img width="500" src="https://user-images.githubusercontent.com/66231761/154805314-389bdf05-3f74-4da3-9eaf-3a5d0230b110.png">  

# 스택 영역

- 변수, 메서드 정보가 stack frame 으로 위로 쌓이면서 저장됨
- 맨 마지막에 호출된 함수부터 LIFO 순서로 처리
- 할당과 해제가 자동으로 처리
- threadsafe
- OS로 부터 할당받는 메모리 크기가 고정(정적 할당)
- default size: 512KB

# Program Counter 레지스터

- 바이트코드에서 다음 실행될 명령어의 주소를 기록하는 영역

# 네이티브 메서드 스택

- 성능 향상을 목적으로 C나 C++ 같이 다른 언어로 작성된 코드가 저장되는 영역

# 왜 자바는 stack과 힙영역을 나눴을까? 그냥 stack에 다 넣으면 안되나

- stack은 heap에 비해 메모리 용량이 제한적이므로 가벼운 타입만 저장하고 heap에는 상대적으로 무거운 Object 타입 객체를 저장한다
- 객체는 stack에 저장할 수 없는 절대 원칙
- 개발자가 메모리 관리를 안해도 되도록 함

# 스택의 단점

- 스택은 힙에 비해 크기가 작아서 메모리 크기를 초과하면 스택오버플로우가 발생할 수 있다

# 힙의 단점

- 메모리 관리가 안되면 사용하지 않는 데이터가 띄엄띄엄 쌓이는 단편화나 메모리 누수(낭비)가 발생할 수 있다

# 힙 메모리 영역이 자료구조의 힙과 같을까?

- 전혀 상관없다
- 힙보다는 pool이 더 맞는 용어

# 왜 스택은 힙보다 빠를까?

- 스택은 정적 할당으로 고정된 크기의 메모리를 할당하므로 동적으로 메모리를 수시로 반납할 필요가 없고, 스택 포인터 위치만 변경하고 스택 포인터가 가리키는 공간을 덮어쓰는 방법을 사용한다
- 스택은 정적 할당이므로 런타임에 남은 메모리가 얼마인지 계산할 필요없이 그저 할당만 해주면 된다

---

# GC

- 가비지 컬렉터는 메모리에서 더이상 사용되지 않는 객체들을 지속적으로 찾아내서 제거하는 역할을 함
- Mark & sweep 방식을 사용
    - mark는 사용되는(reachable) 객체와 사용되지 않는(unreachable) 객체를 구분하는 것
    - sweep은 사용되지 않는 객체를 지우는 것

# 힙 메모리영역

<img width="600" src="https://user-images.githubusercontent.com/66231761/154805353-e5018ae6-3d99-4162-b85f-096b5b1a16ad.png">  

<img width="600" src="https://user-images.githubusercontent.com/66231761/154805385-a00de699-5b1c-4188-8f22-21ac7af5fdc4.png">  

- MinorGC: Eden영역이 다 차서 mark & sweep후 남은 reachable 객체를 s0나 s1로 이동시키는 것(단 s0나 s1중 하나는 비어있어야 함)
- Promotion: s0와 s1간 이동시 age가 1씩 증가하는데 일정 age 이상이 되면 old generation으로 이동함
- MajorGC: old generation영역이 다 차서 old generation을 대상으로 하는 GC

# Parallel GC

<img width="500" src="https://user-images.githubusercontent.com/66231761/154805427-9669c331-526c-45dd-bc4e-507420dc1766.png">  

- mark 과정을 단일 스레드로 처리한 Serial GC와 다르게 GC 스레드를 멀티 스레드로 처리하는 방법
- 시간당 처리량을 높여서 stop-the-world 시간을 단축함
- Java 8 버전의 Default GC
- stop-the-world
    - 사용하지 않는 객체를 찾기 위해 전체 스레드를 멈추는 현상
    - java application이 멈춤

# CMS(Concurrent Mark Sweep) GC

- stop-the-world 시간을 최소화하기 위한 GC
- 과정이 복잡하고 CPU 사용량이 높으며 Compact를 제공하지 않아 메모리 단편화로 인한 메모리 누수가 발생할 수 있다

<img width="500" src="https://user-images.githubusercontent.com/66231761/154805446-31491bf7-2bd0-4143-b303-bcedec99cda5.png">  

- initial mark
    - gc root가 참조하는 객체만 마킹
- concurrent mark
    - stop-the-world 없이 marking 처리
- remark
    - sweep 직전에 추가로 unreachable 객체를 마킹
- concurrent sweep
    - stop-the-world 없이 sweep 처리

# G1(Garbage First) GC

<img width="500" src="https://user-images.githubusercontent.com/66231761/154805488-6136ce55-ec0d-4e68-b2aa-9bc02cb6a723.png">  

- 기술의 발전으로 메모리가 커짐에 따라 GC가 탐색할 범위도 증가했는데, STW 시간을 줄이기 위한 GC
- Java 9 부터 Default로 채택된 GC
- stop-the-world를 최소화하고 병렬(concurrent)과 compact를 지원하는 GC
- 바둑판처럼 region으로 나누고 상황에 따라 Eden, Survivor, old 영역으로 사용
- 사용되는 객체가 가장 적은 region을 쓰레기로 판단해서 비워주고 메모리를 반납함
- compact는 아직 비어있지 않은 region을 새 region에 copy해서 진행

---

# Java Collection

<img width="500" src="https://user-images.githubusercontent.com/66231761/154805533-e04af11d-92f7-4129-a86f-a4b5b8b10b08.png">  

# Hash vs Tree

- Hash
    - Hash 자료구조, Hash Function을 지원한다는 의미
        - 랜덤으로 받은 파라미터값을 고정된 길이의 유니크한 문자(Hash Value)로 반환하는 메서드
        - hash value를 key로 값을 저장해서 탐색이 빠름 O(1)
    - 순서를 보장하지 못함
        - 단, LinkedHashMap, LinkedHashSet은 순서 보장
- Tree
    - 이진 트리를 사용한다는 의미
        - 계층적이며 자동 오름차순 정렬되있어서 sub tree로 그룹핑해서 범위 검색하기 용이함
    - 다양한 정렬 방법을 지원하고 순서를 보장함
    - 접근 속도가 hash보단 느림 O(logN)
- List는 애초에 순서를 보장하기 때문에 Hash, Tree 용어가 붙지 않음

# Synchronize

- 멀티 스레드 환경에서 특정 스레드가 실행중일 때 lock을 걸어 다른 스레드가 접근 못하게 막는 것

# 왜 동기화를 지원하는 구현체는 사용하지 않을까

- lock을 걸고 해제하는 오버헤드가 발생해서 단일 스레드에선 Vector나 HashTable이 사용되지 않고 멀티스레드 환경에선 동시성이 떨어짐
- Collections.SynchronizedList()나 java.util.concurrent 등 대체재 존재
- legacy 하위호환을 위해서만 존재

# HashTable vs ConcurrentHashMap

- HashTable
    - map 읽기/쓰기 작업에 lock을 건다
    - lock이 걸린상태에서 다른 스레드가 접근하면 ConcurrentModificationException 발생
    - Enumerator로 출력
- ConcurrentHashMap
    - map을 여러 세그먼트로 나누고 map 전체가 아닌 segment에 lock을 거는 방식
    - Iterator로 출력
    - concurrencyLevel = 16
        - default 세그먼트 수
        - 쓰기 스레드와 동시에 수용가능한 수
    - key 또는 value에 null값을 허용하지 않음
    - loadFactory = 0.75f
        - resizing 하는 시점(75% 일때 동작)
    - initial Capacity = 10
        - 초기 Entry 적재 용량

# Reference

- [https://jeong-pro.tistory.com/148](https://jeong-pro.tistory.com/148)
- [https://beststar-1.tistory.com/15](https://beststar-1.tistory.com/15)
- [https://www.geeksforgeeks.org/how-does-concurrenthashmap-achieve-thread-safety-in-java/](https://www.geeksforgeeks.org/how-does-concurrenthashmap-achieve-thread-safety-in-java/)
