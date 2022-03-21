# 6th-week

# Spring Self-Invocation문제

- 동일한 클래스안에서 트랜잭션이 적용된 메서드를 호출시 트랜잭션이 적용되지 않는 문제

```java
public void saveAB(A a, B b)
{
    saveA(a);
    saveB(b);
}

@Transactional
public void saveA(A a)
{
    dao.saveA(a);
}

@Transactional
public void saveB(B b)
{
    dao.saveB(b);
}
```

# 왜 Self-Invoation가 발생할까?

- Spring AOP는 프록시 기반이므로 대상 객체를 프록시 객체로 생성하여 메서드를 호출하는 쪽의 접근을 제어하는 방식으로 동작함
- @Transactional도 AOP로 동작하기 때문에 self invocation시 적용 안됨
- 클래스 내부에서 트랜잭션 메서드를 호출하는 것은 프록시 참조가 아니라 this 참조이므로 트랜잭션이 적용되지 않음

# 어떻게하면 self invocation을 피할 수 있을까?

- Spring AOP대신 AspectJ를 사용
- 객체의 책임을 적절히 분리해서 외부 호출을 통해 객체끼리 통신하도록 설계한다
    - 즉, 애초에 self invocation이 발생할 상황을 차단함

# AOP란?

- Aspect Oriented Programming

# Spring AOP

- 프록시 기반

```java
public class SimplePojo implements Pojo {

    public void foo() {
        // this next method invocation is a direct call on the 'this' reference
        this.bar();
    }

    public void bar() {
        // some logic...
    }
}
```

![스크린샷 2022-03-21 오후 9 09 12](https://user-images.githubusercontent.com/66231761/159258112-486c6345-295d-4fe1-bd32-dce35e84efaa.png)

```java
public class Main {

    public static void main(String[] args) {
        Pojo pojo = new SimplePojo();
        // this is a direct method call on the 'pojo' reference
        pojo.foo();
    }
}
```

![스크린샷 2022-03-21 오후 9 09 31](https://user-images.githubusercontent.com/66231761/159258164-f1ef0a6d-8442-4aa9-b09f-8f196876201e.png)

---

# JWT란?

- 서버와 클라이언트간 통신시 인증을 구현하는 방법
- 서버는 인증 정보를 따로 관리하지 않고 단지 클라이언트에 토큰을 발급한다
- 전자 서명되어 변조 여부를 체크할 수 있다
- URL-safe(URL로 사용할 수 있는 문자로만 구성)
- 비대칭/공개키 암호화 방식 사용

# 왜 JWT를 쓸까?

- 서버에 세션을 유지할 필요가 없다
    - 서버 메모리의 세션 저장소에서 인증 정보를 관리하는 방식과 다르게 사용자가 로그인했는지 안했는지 신경쓸 필요가 없다
    - JWT 자체에 모든 인증 정보가 포함되어 있기 때문에 별도의 메모리 공간을 필요로 하지 않는다
- 안정적
    - 정보가 서명되어 있기 때문에 통신 도중에 정보가 탈취되어 조작되었는지 검증 가능
- 분산 서버에 적합
    - 세션 방식은 서버마다 인증 정보가 다 다른데 jwt는 애초에 서버에서 인증 정보를 관리하지 않기 때문에 분산 서버에 적합
- MSA에 적합
    
    <img width='700' src='https://user-images.githubusercontent.com/66231761/159258206-00f03b43-7882-4868-a89d-14e551cbd183.png'/>

    - 기존의 방식은 분산된 마이크로 서비스에 접속할 때마다 인증 서비스에 참조 해야하는데
    
    <img width='700' src='https://user-images.githubusercontent.com/66231761/159258546-06e6f61c-8b5e-473b-b76b-ac9252ac63bc.png'/>
        
    - jwt은 인증 서비스는 단지 토큰 발급만 하고 각 마이크로 서비스에서 토큰을 검증할 수 있다

# JWT을 어디에 쓸까?

- Authorization
    - 한번 로그인 하면 jwt를 발급받고, 그 이후로 모든 요청에 jwt가 헤더에 포함된다
- 정보 교환
    - jwt은 전자 서명을 통해 변조되지 않음을 보장한다

# JWT Process

<img width='700' src='https://user-images.githubusercontent.com/66231761/159258829-fb3ab946-b818-448f-aca3-e2dbde9771b2.png'/>

- 처음 사용자 등록시 Access Token과 Refresh Token을 둘다 발급함
- jwt에 필요한 모든 정보를 포함하기 때문에 데이터베이스 같은 서버와의 커뮤니케이션 오버 헤드를 최소화할 수 있음
- CORS는 쿠키를 사용하지 않기 때문에 JWT를 채용한 인증은 두 도메인에서 api를 제공하더라도 문제가 발생하지 않음

# Bearer란?

- RFC 6750 표준에서 정한 인증 타입
- JWT, OAuth가 해당

# 양방향 암호화 알고리즘 vs 단방향 암호화 알고리즘

- 양방향 암호화 알고리즘
    - 암호화, 복호화 둘다 지원
- 단방향 암호화 알고리즘
    - 암호화나 복호화 중 하나만 지원

# 대칭키 암호화 vs 비대칭키 암호화

- 대칭키 암호화 방식
    - 암호화, 복호화에 사용되는 키가 동일
- 비대칭키 암호화 방식
    - 암호화, 복호화에 사용되는 키가 다름

# 비대칭키(공개키) 암호화

- Public Key(공개키)와 Private Key(개인키)로 나누어 암호화, 복호화를 전담함
- public key 암호화 & private key 복호화 방식
    
    <img width='700' src='https://user-images.githubusercontent.com/66231761/159258872-1efded16-089b-45cc-b3bf-1b7a02c1b040.png'/>

    - 데이터를 안전하게 전송하기 위해 사용
    - 사용자가 민감한 데이터를 전송할 때 공개키로 암호화하고 서버측에서 개인키로 복호화함
        - 공격자가 탈취하더라도 개인키가 없기 때문에 데이터를 볼 수 없다
- public key 복호화 & private key 암호화 방식
    
    <img width='700' src='https://user-images.githubusercontent.com/66231761/159258887-1ef69f96-6ff1-4657-bfbe-7b9e37c866e6.png'/>

    - 특정 서버가 신뢰할 수 있는 단체라는 것을 인증하기 위해 사용
    - 서버는 개인키로 암호화한 인증서를 유저에게 주고 유저는 CA(공인 인증 기관)에서 제공하는 공개키로 복호화해서 복호화에 성공하면 해당 서버를 신뢰할 수 있다

# 인코딩 vs 디코딩

- 인코딩
    - 문자열이나 기호를 컴퓨터가 이해할 수 있으며 보다 압축화된 코드로 변환하는 과정
- 디코딩
    - 인코딩된 코드를 반대로 문자열로 변환하는 과정
- 인코딩 방식
    - ASCII, URL, BASE64 등

# Access Token & Refresh Token

- Access Token
    - 생명주기가 짧은 토큰
- Refresh Token
    - 생명주기가 긴 토큰
    - Access Token 만료시 재발급하기 위한 토큰
- Access Token 토큰 탈취를 방지하기 위한 방식이다
- access token과 refresh token을 db에서 관리해야하는 오버헤드 발생

# Refresh Token을 사용하는 이유

- JWT 탈취 문제 해결
    - JWT는 stateless하기 때문에 서버측에서 이 토큰을 갖고 있는 클라이언트가 클라이언트 본인이 맞는지 확인할 수 없다
    - 만약 Access Token이 한 시간이라고 한다면 해당 Access Token이 탈취되었을 때 서버는 Access Token이 만료되기 전까지 공격자와 진짜 유저를 구별할 수 없다.
        - 따라서 Access Token의 생명 주기를 더 짧게 세팅하고 상대적으로 생명주기가 긴 Refresh Token을 같이 사용한다
        - 공격자는 JWT를 탈취하더라도 유효 시간이 짧아서 사용불가하고 정상 클라이언트는 Refresh Token으로 Access Token을 새로 발급받는다
- Refresh Tokend이 만료될 때까지 사용자는 로그인이 유지됨
- 같은 사용자가 여러 디바이스로 로그인 하는 경우 하나의 디바이스만 적용 가능
    - 디바이스마다 Access Token & Refresh Token 쌍이 필요하다

# Refresh Token을 탈취당하는 문제

- Refresh Token 마저 탈취당하는 문제를 해결하기 위해 Refresh Token Rotation방식을 적용 가능
    - Refresh Token으로 Access Token 재발급시 Refresh Token도 재발급함

# 시나리오

<img width='700' src='https://user-images.githubusercontent.com/66231761/159258915-11d2a19b-7129-4aee-b19e-89d3001873db.png'/>

- 로그인 요청
    - 성공시, access token & refresh token 발급
- 로그아웃 요청
    - access token과 refresh token 모두 폐기
- 토큰 검증
    - case1 : access token과 refresh token 모두 만료된 경우 → 로그인 요청
    - case2 : access token 만료, refresh token 유효한 경우
        - refresh token 유효성 체크 & DB에 저장한 refresh token과 비교해서 같으면 access token 재발급

# Ref

- [https://woodcock.tistory.com/30](https://woodcock.tistory.com/30)
- [https://cotak.tistory.com/102](https://cotak.tistory.com/102)
- [https://velog.io/@park2348190/JWT에서-Refresh-Token은-왜-필요한가](https://velog.io/@park2348190/JWT%EC%97%90%EC%84%9C-Refresh-Token%EC%9D%80-%EC%99%9C-%ED%95%84%EC%9A%94%ED%95%9C%EA%B0%80)
- [https://myjamong.tistory.com/293](https://myjamong.tistory.com/293)
- [https://www.hides.kr/1093?category=585637](https://www.hides.kr/1093?category=585637)
- [https://llshl.tistory.com/32](https://llshl.tistory.com/32)
