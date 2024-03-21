# 섹션 16. Spring Security로 Spring Boot 앱 보호하기

## 0단계 - Spring Security 시작하기

## 1단계 - 보안의 기초 이해하기

- resources
- identities
- Authentication(is it the right user?)
- Authorization(do they have the right access?)

## 2단계 - 보안의 원칙 이해하기

1. Trust Nothing
2. Assign LEast Privileges
3. Have Complete Mediation 
4. Have Defense In depth 
5. Have Economy Of Mechanism
6. Ensure Openness Of Design

## 3단계 - Spring Security 시작하기

- Spring Security
    - Filter Chain
    - Authentication managers
    - Authentication providers
- Request → Dispatcher Servlet → Controller

## 4단계 - Spring Security 기본 설정 살펴보기

- Spring Security Filter Chain
    - 인증
    - 권한 부여
    - Other features
        - CORS
        - CSRF

## 5단계 - Spring Security용 Spring Boot 프로젝트 생성하기

## 6단계 - Spring Security 살펴보기 - 폼 인증

- Everything is authenticated
    - 리소스 지금 없는 것도 보호함
- 폼 인증
    - Session Cookie
    - login page
    - logout page

## 7단계 - Spring Security 살펴보기 - 기본 인증

- Basic Auth Authorization Header
    - No Expire date

## 8단계 - Spring Security 살펴보기 - 크로스 사이트 요청 위조

- 다른 사이트로 이동 시 쿠키를 이용해 요청 가로채기

## 9단계 - Spring Security 살펴보기 - REST API에서의 CSRF

- POST와 PUT 요청에는 CSRF 토큰이 필요함

## 10단계 - CSRF를 사용하지 않도록 Spring Security 설정 생성하기

## 11단계 - Spring Security 살펴보기 - CORS 시작하기

- CORS
    - Global Configuration
    - Local Configuration

## 12단계 - Spring Security 살펴보기 - 메모리에 사용자 자격증명 저장하기

## 13단계 - Spring Security 살펴보기 - JDBC를 사용해 사용자 자격증명 저장하기

## 14단계 - 인코딩, 해싱, 암호화 이해하기

- 인코딩: 데이터를 원래 형식에서 다른 데이터로 변경
    - 원래 데이터 구할 수 있음
    - 목적이 데이터 보안이 아님
    - 압축이 목적
    - Base64
- 해싱: 데이터를 해시 문자열로 변환
    - 해시에서 원래 데이터를 구할 수 없음
    - 데이터 무결성을 검증하는 것이 목적
    - bcrypt, scrypt
- 암호화
    - 키나 패스워드를 이용해 암호화
    - RSA

## 15단계 - Spring Security 살펴보기 - Bcrypt 인코딩 암호 저장하기

- SHA256은 더이상 안전하지 않음
    - 이제 계산이가능함
- 단방향 함수를 사용하는 것을 추천
    - bcrypt, scrypt

## 16단계 - JWT 인증 시작하기

- JWT
    - Header
    - Payload
    - Signature
- 대칭 키 암호화
- 비대칭 키 암호화

## 17단계 - Spring Security와 Spring Boot로 JWT 인증 설정하기 - 2

## 18단계 - Spring Security와 Spring Boot로 JWT 인증 설정하기 - 2

## 19단계 - Spring Security와 Spring Boot로 JWT 리소스 설정하기 - 1

- 기본 인증을 이용해 JWT 토근 만들기

## 20단계 - Spring Security와 Spring Boot로 JWT 리소스 설정하기 - 2

## 21단계 - Spring Security 인증이란?

- Spring Security Filter chain
- UserDetailsService = Core interface to load user data

## 22단계 - Spring Security 인증의 이모저모

- 전역 보안
- 메서드 보안

## 23단계 - Spring Security를 이용한 Spring Boot OAuth 프로젝트 만들기

## 24단계 - Spring Boot와 OAuth2 시작하기 - Google을 이용한 로그인

## 25단계 - Spring  Security로 Spring Boot 앱 보호하기