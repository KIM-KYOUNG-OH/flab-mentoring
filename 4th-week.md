# 로그인 API 개발

- [https://github.com/KIM-KYOUNG-OH/login-api](https://github.com/KIM-KYOUNG-OH/login-api)

# 인증(Authentication)

- 시스템 자체 접근 대한 신분 확인이 목적인 신원 확인 프로세스
- 인가 이전에 수행
- who are you?

# 인가

- 시스템 자원에 대한 접근 권한 확인이 목적인 권한 확인 프로세스
- 인증 이후에 수행
- what permissions do you have?

# 로그인 인증 방식

- 쿠키
    - 요청마다 자동으로 쿠키가 담겨서 제공
    - 쿠키는 어디에 저장될까?
        - 클라이언트
    - 라이프사이클
        - 만료시간에 따라 다르고 브라우저가 종료되더라도 남아있을 수 있음
- 세션
    - HttpSession 으로 sessionId을 생성하고 JSESSIONID 쿠키를 자동 생성
    - 특정 유저와 서버의 커넥션마다 세션이 생성되는 것인가?
    - 여러 유저가 서버의 세션을 공유하는 것인가?
    - 세션은 어디에 저장되는가?
        - 서버
        - 메모리 내부 톰캣의 세션 저장소에 저장됨
    - 라이프사이클
        - 만료시간에 따라 다르고 브라우저가 로그인하면 생성되고 브라우저를 종료하면 삭제됨
- JWT 토큰
    - 핵심은 세션 방식과 다르게 서버측에서 로그인 정보를 기억해놓지 않고 단지 토큰이 유효한지만 검증한다는 것
    - 사용자의 상태를 관리하지 않는(stateless) 토큰은 한 기기에서만 로그인할 수 있도록 제한할 수 없다
        - refresh 토큰과 access 토큰을 사용하면 가능
            - refresh 토큰은 DB에 저장되서 수명이 길다
            - access 토큰은 수명이 짧아서 탈취당해도 안전함
    - 구성
    
        <img width="660" alt="스크린샷 2022-03-09 오후 8 49 30" src="https://user-images.githubusercontent.com/66231761/157436284-3835afa5-70dc-4f5b-b8c4-320b97a5aa02.png">  

        - header
            - 아래의 두 가지 정보가 담김
                - typ: 토큰의 타입을 지정
                - alg: 어떤 해싱 알고리즘을 사용할지 지정
            
            ```json
            { 
              "typ": "JWT", 
              "alg": "HS256" 
            }
            ```
            
        - payload
            - 토큰에 담을 정보
            - key-value 형태로 저장되며 한 쌍의 정보를 Claim이라고 부름
            - registered claim, public claim, private claim으로 나뉨
                - **iss** : 토큰 발급자 (issuer)
                - **sub** : 토큰 제목 (subject)
                - **aud** : 토큰 대상자 (audience)
                - **exp** : 토큰의 만료시간 (expiraton), 시간은 NumericDate 형식으로 되어있어야 하며 (예: 1480849147370) 언제나 현재 시간보다 이후로 설정돼야한다.
                - **nbf** : Not Before 를 의미하며, 토큰의 활성 날짜와 비슷한 개념. 여기에도 NumericDate 형식으로 날짜를 지정하며, 이 날짜가 지나기 전까지는 토큰이 처리되지 않는다.
                - **iat** : 토큰이 발급된 시간 (issued at), 이 값을 사용하여 토큰의 age 가 얼마나 되었는지 판단 할 수 있다.
                - **jti** : JWT의 고유 식별자로서, 주로 중복적인 처리를 방지하기 위하여 사용된다. 일회용 토큰에 사용하면 유용.
            - 키 충돌을 주의하자
            
            ```json
            {
                "iss": "llshl.com",
                "exp": "1485270000000",
                "https://llshl.com/jwt_claims/is_admin": true,
                "userId": "11028373727102",
                "username": "llshl"
            }
            ```
            
        - signature
            - header와 payload값을 서버의 private key와 함께 계산한 결과값이 signature와 일치하는 지 확인
- 고려사항
    - 유효기간
    - 로그인 정보 탈취
    - 로그인 정보 노출
    - 에러로 인한 정보 손실

# 다중 서버 환경에서 인증 정보 저장하기

- 다중 서버는 사용자가 많아짐에 따라 여러 서버에 트래픽을 분산하기 위해 사용
- 데이터 정합성 이슈
    - 각 서버별로 세션이 존재하기 때문에 요청하는 서버가 달라지면 인증이 처리되지 않음
    - sticky session, session clustering, session storage 방식으로 문제 해결

# Sticky Session

- 사용자의 인증 정보가 생성된 서버하고만 고정으로 통신함
- 특정 서버에만 트래픽 부하가 몰릴 수 있다

# Session Clustering

- 여러 개의 서버가 하나의 서버처럼 움직이는 방식
- all to all 방식
    - 하나의 서버에 세션 정보를 저장하면 나머지 다른 서버에도 같은 세션 정보를 복사해서 저장
- 모든 서버에 세션 정보를 복사해야하는 네트워크 요청 오버헤드 발생
- 메모리 비효율
- 서버를 stateless하게 유지할 수 없다
- ex) Tomcat의 deltamanager

# Session Storage

- 별도의 세션 서버를 두고 모든 서버가 공유하는 방식
- 여러 서버를 stateless하게 유지할 수 있다
- 트래픽 부하를 줄일 수 있다
- ex) inmemory DB Redis
