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

1. 데이터 조회시 casting 비용이 발생하지 않음
    - non-generic 클래스엔 Object 타입의 모든 인스턴스를 허용하기 때문에 개별 데이터 조회마다 casting 비용이 발생한다
    - generic 클래스는 지정된 타입의 데이터만 가짐을 보장하기 때문에 casting 비용이 발생하지 않음
2. 코드 재활용성을 높일 수 있다
    - 제네릭 메서드나 클래스를 정의할 때 와일드카드(T)를 사용하면 다양한 reference 타입 변수를 허용하기 때문에 중복코드를 줄이고 코드 재활용성을 높일 수 있다
    - 단, T는 reference 타입만 가능

```java
public <T> void genericsMethod (T data) {...}
```

# 와일드 카드

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

# Ref

- [https://www.geeksforgeeks.org/generics-in-java/?ref=gcse](https://www.geeksforgeeks.org/generics-in-java/?ref=gcse)
