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
