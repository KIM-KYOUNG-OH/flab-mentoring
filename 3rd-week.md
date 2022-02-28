# Permanent Generation(Permgen)

- Heap 메모리 영역에서 별도로 분리된 영역
- 메모리 최대 크기가 64~82MB이며 메모리 크기가 고정
- 메모리 auto increase를 지원하지 않음
- 고정된 메모리 크기로 인한 Memory Leak이 발생
- Java 8 버전부터 Metaspace로 완전히 대체되었다

# Metaspace

- 메모리 auto increase를 지원함
- OS에 의해 Native memory에 할당
- metaspace가 꽉차면 자동으로 GC를 수행함
- OutOfMemory 에러 빈도가 줄어듬

# Metaspace에 저장되는 데이터

- 정적 데이터
- metadata(ClassLoader가 load한 class 데이터)
- 런타임 상수 풀
- 메서드 영역이 포함됨
    - 클래스 정보(메서드, 생성자, 변수...)

# Constant Pool

- heap에 속하는 작은 캐시 영역
- 문자열을 선언하면 먼저 상수풀에 있는지 확인하고 없다면 새로 할당한다
- Java 6 버전까지는 PermGen 영역에 속했지만, Java 7 버전 부터 메인 힙 영역으로 옮겨짐
    - PermGen은 메모리 크기가 고정이라 제한적이기 때문

# String s = “welcome”; vs String s = new String(”welcome”);

- new 예약어를 사용하면 String 객체가 상수 풀이 아닌 힙 영역에 생성됨

# 왜 Permgen은 Metaspace로 대체되었나?

- Permgen은 메모리 크기가 고정적이기 때문에 GC를 수행해도 OOME(OutOfMemoryError)가 발생한다
- Metaspace는 메모리가 꽉차면 자동으로 GC를 수행하고 메모리가 auto increase를 지원함

---

# Generic이란?

- 타입을 파라미터화한 것
- 메서드, 클래스, 인터페이스에 적용 가능
- 타입안정성과 코드 재활용성을 위해 사용

# 왜 Generic을 사용할까?

1. 컴파일 타임에 에러가 발생하게 해줌

```java
class Test
{
    public static void main(String[] args)
    {
        ArrayList al = new ArrayList();

        al.add("Sachin");
        al.add("Rahul");
        al.add(10); // Compiler allows this

        String s1 = (String)al.get(0);
        String s2 = (String)al.get(1);

        // Causes Runtime Exception
        String s3 = (String)al.get(2);
    }
}
```

```java
class Test
{
    public static void main(String[] args)
    {
        ArrayList <String> al = new ArrayList<String> ();

        al.add("Sachin");
        al.add("Rahul");

        // Now Compiler doesn't allow this
        al.add(10);

        String s1 = (String)al.get(0);
        String s2 = (String)al.get(1);
        String s3 = (String)al.get(2);
    }
}
```

2. 데이터 조회시 casting 비용이 발생하지 않음
    - non-generic 클래스엔 Object 타입의 모든 인스턴스를 허용하기 때문에 개별 데이터 조회마다 casting 비용이 발생한다
    - generic 클래스는 지정된 타입의 데이터만 가짐을 보장하기 때문에 casting 비용이 발생하지 않음
3. 코드 재활용성을 높일 수 있다
    - 제네릭 메서드나 클래스를 정의할 때 와일드카드(T)를 사용하면 다양한 reference 타입 변수를 허용하기 때문에 중복코드를 줄이고 코드 재활용성을 높일 수 있다
    - 단, T는 reference 타입만 가능

```java
public <T> void genericsMethod (T data) {...}
```

---

# Serialization

<img width="650" alt="스크린샷 2022-02-25 오후 8 58 34" src="https://user-images.githubusercontent.com/66231761/155711573-27f48dd0-f11c-4066-a587-df08e98bde0b.png">  

- 직렬화는 자바 객체를 ByteStream으로 변경하는 것을 말함
- Deserialization은 반대로 ByteStream을 자바 객체로 변경하는 것

# ByteStream이란?

- ByteStream은 platform에 독립적이다
    - 어떠한 플랫폼을 쓰더라도 byteStream을 사용한다

# 왜 직렬화를 사용할까?

- 객체의 상태를 영속화할 수 있다
- 네트워크 상에 객체를 옮길 수 있다

# Serializable

- 직렬화 객체는 java.io.Serializable 인터페이스를 implements 해야함
    - Serializable을 implements하지 않으면 NotSerializableException이 발생함
- Serializable 인터페이스는 아무런 메서드가 없는 Marker interface이다
    - 단순 타입 체크를 위해 사용
- 단, transient나 static 데이터는 Serialization process에 포함할 수 없음
    - 역직렬화시 transient 데이터는 해당 타입의 default값을 조회하고 static 데이터는 현재 값을 가져옴
- writeObject()
    - 객체 직렬화 메서드
    - ObjectOutputStream 클래스의 멤버 메서드
- readObject()
    - 객체 역직렬화 메서드
    - ObjectInputStream 클래스의 멤버 메서드

---  

# Optional 이란?

- Optional는 null일 수도 있는 값을 담는 컨테이너 객체이다

# 왜 Optional을 쓸까?

- NullPointerException을 회피하기 위한 클래스
- null 체크 코드를 줄여서 가독성을 높임
- 값을 반환받을 시점에 바로 널 체크를 하기위해 사용
- 값이 null이면 default 값을 반환하거나 에러를 발생시키기 위해 사용

# Optional 객체 생성

- Optional 유틸클래스의 static 메서드를 사용해서 Optional 객체를 생성함
    - of(T value)
        - non-null 값을 가지는 Optional 객체를 반환
        - 해당 객체에 null을 저장하려하면 NullPointerException이 발생
    - ofNullable(T value)
        - nullable한 값을 가지는 Optional 객체를 반환
    - empty()
        - 비어있는 Optional 객체를 반환

# Optional 객체 접근

- T orElse(T other)
    - 값이 존재하면 그 값을 반환하고, 없으면 파라미터값을 반환
- T orElseGet(Supplier<? extends T> other)
    - 값이 존재하면 그 값을 반환하고, 없으면 파라미터로 전달된 람다 표현식의 결과값을 반환
- <X extends Throwable> T orElseThrow(Supplier<? extends X>  exceptionSupplier)
    - 값이 존재하면 그 값을 반환하고, 없으면 파라미터로 전달된 예외를 발생

# 왜 Optional은 Serializable을 지원하지 않을까?

- Optional은 애초에 리턴 타입으로 사용하기 위한 목적이지 Optional을 파라미터나 field로 쓰는 방식은 안티 패턴이다

---

# Java IO

- Stream 기반 패키지

    <img width="872" alt="스크린샷 2022-02-28 오후 8 03 25" src="https://user-images.githubusercontent.com/66231761/155972428-1bd856ac-d89a-4827-8a01-285456440ca6.png">  

- blocking, synchronous

# Java NIO(New IO)

- JDK 4 버전에 나옴
- 기존 IO API보다 빠른 성능으로 대체됨
- 버퍼 기반 패키지

    <img width="883" alt="스크린샷 2022-02-28 오후 8 03 55" src="https://user-images.githubusercontent.com/66231761/155972490-a358a56a-4b4c-4746-aadf-b2d749c4bc6a.png">  

- non-blocking, asynchronous

# Channel

- Channel는 버퍼에 읽기/쓰기 작업을 하는 주체이다
- Channel 하나는 싱글 스레드를 처리한다

# selector

<img width="879" alt="스크린샷 2022-02-28 오후 8 04 30" src="https://user-images.githubusercontent.com/66231761/155972567-c3747bd5-0305-4e81-ba24-e6e1739b7fee.png">  

- 멀티 Channel 환경에서 준비된 Channel을 선택해서 동작시킴

# Blocking vs Non-Blocking

- 제어의 관점
- Blocking
    - A스레드 작업중 B스레드에 요청시 B스레드가 끝날 때까지 A스레드가 기다림
- Non-Blocking
    - A스레드 작업중 B스레드에 요청시 B스레드가 끝날 때까지 A스레드가 기다리지 않고 다른 일을 함

# Sync vs Async

- 결과 처리 순서의 관점
- Sync
    - A스레드가 B스레드에게 요청한 작업을 B스레드가 완료해서 결과를 받은 즉시 처리함
- Async
    - A스레드가 B스레드에게 요청한 작업을 B스레드가 완료해서 결과를 받은 후 나중에 처리함

# 왜 java IO 보다 NIO가 더 빠를까?

- non-blocking 동작을 지원한다
    - 버퍼는 작업을 즉시 처리하지 않고 지연 처리한다
- 다중 Channel과 Selector를 이용해서 멀티 스레드 IO 작업을 처리

---

# Spring의 장점

- POJO 객체를 사용하므로 가볍고 프레임워크에 종속적이지 않다
- xml이나 annotation을 이용해서 유연하게 configuration을 변경할 수 있다
- AOP를 지원
- DI를 통해 test가 쉽다

# Spring의 단점

- 구조가 복잡하고 높은 이해도를 요구
- 개발자에게 많은 선택 사항을 줘서 오히려 잘못된 결정이나 개발 지연을 유발할 수 있다

# POJO란?

- extends, implements, annotation이 없는 자바 순수 객체
- 보통 엔티티를 정의할 때 사용

# 왜 POJO를 쓸까?

- spring에 의존하지 않고 다른 프레임워크에서도 재사용 가능
- 과거 EJB 시절 엔티티가 기술에 의존적으로 작성되서 변경이 어려웠는데 POJO로 이를 보완

---

# 왜 DI와 IOC를 사용할까?

- 별도의 설정자에게 DI 권한을 위임하여 개발자가 객체지향에 더 집중해서 개발할 수 있도록 도와줌

# @Autowired를 지양해야 하는 이유

- 주입받을 필드를 final로 선언할 수 없다
    - 순환 참조 에러를 컴파일 타임에 캐치할 수 없다
- final 사용가능
    - 런타임에 불변 보장
- 테스트 용이성
    - 생성자 주입은 스프링 컨테이너에 의존하지 않고 테스트 가능

```java
@ExtendWith(SpringExtension.class)
@ContextConfiguration(classes = SpringInjectionApplication.class)
class ConstructorInjectionTest {
    @Autowired
    private Water water;

    @Test
    @DisplayName("생성자 주입 테스트 코드")
    void constructor_injection() {
        assertThat(water).isNotNull();
        assertThat(water.getTeaBag()).isNotNull();
        assertThat(water.getTeaBag()).isInstanceOf(Chamomile.class);
    }
}
```

```java
class ConstructorInjectionTest {
    @Test
    @DisplayName("생성자 주입 사용 시, 스프링 컨테이너에 의존하지 않고 테스트할 수 있다.")
    void constructor_injection_without_dependence() {
        Water water = new Water(new GreenTea());
        assertThat(water).isNotNull();
        assertThat(water.getTeaBag()).isNotNull();
        assertThat(water.getTeaBag()).isInstanceOf(GreenTea.class);
    }
}
```

# AOP란?

- AOP는 핵심 관점을 기준으로 프로그램을 여러 개의 모듈로 나누어 개발하는 방식을 의미함
- ex) 공통되어 반복되는 로직인 트랜잭션, 로깅, 보안 등에 관련된 로직을 따로 분리

# 왜 AOP를 사용할까?

- cross-cutting concern 해결을 목적으로 함(관심사 분리)

# Ref

- [https://www.geeksforgeeks.org/generics-in-java/?ref=gcse](https://www.geeksforgeeks.org/generics-in-java/?ref=gcse)
- [http://www.tcpschool.com/java/java_io_stream](http://www.tcpschool.com/java/java_io_stream)
- [https://dzone.com/articles/optional-anti-patterns](https://dzone.com/articles/optional-anti-patterns)
- [https://skywell.software/blog/java-spring-framework-pros-cons-mistakes/](https://skywell.software/blog/java-spring-framework-pros-cons-mistakes/)
- [https://velog.io/@dahye4321/Constructor-주입을-사용하자](https://velog.io/@dahye4321/Constructor-%EC%A3%BC%EC%9E%85%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EC%9E%90)
