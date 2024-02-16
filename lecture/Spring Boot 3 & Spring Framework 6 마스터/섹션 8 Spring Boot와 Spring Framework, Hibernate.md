# 섹션 8. Spring Boot와 Spring Framework, Hibernate로 Java REST API 생성하기

최종 편집 일시: 2024년 2월 16일 오후 10:11
즐겨찾기: No
프로젝트: (강의)Spring Boot 3 & Spring Framework 6 마스터 (https://www.notion.so/Spring-Boot-3-Spring-Framework-6-afce3387ef4249d6860247e7bc6d8a95?pvs=21)
상태: inbox

## 0단계 - Spring Boot로 REST API 생성하기 - 개요

- 스프링 부트를 사용하는 이유
- 훌륭한 REST API를 빌드하는 법
    - REST API의 우수 사례

## 1단계 - Spring Boot로 REST API 프로젝트 초기화하기

## 2단계 - Spring Boot로 Hello World REST API 생성하기

## 3단계 - Hello World REST API를 업그레이드하여 Bean 반환하기

## 4단계 - 백엔드에서는 무슨 일이 벌어지고 있을까? Spring Boot 스타터와 자동 설정

- request handled?
    1. DispatcherServlet - Front Controller Pattern 
        - mapping servlets: dispatcherServlet urls=[/]
        - Auto Configuration(DispatcherServletAutoConfiguration)
    2. @ResponseBody + JacksonHttpMessageConverters
    3. Who is configuring error mapping?
        - Auto Configuration (ErrorMvcAutoConfiguration)
    4. How are all jars available? 
        - Starter Projects

## 5단계 - 패스 변수로 Hello World REST API 업그레이드하기

## 6단계 - SNS 애플리케이션용 REST API 설계하기

- Key Resources
    - Users
    - Posts
- Key Details
    - User: id, name, birthDate
    - Post: id, description
- GET - Retrieve details of a resource
- POST - Create a new resource
- PUT - Update an existing resource
- PATCH - Update part of a resource
- DELETE - Delete a resource

## 7단계 - 사용자 Bean과 UserDaoService 생성하기

- DAO 객체 - 데이터베이스에 접근하기 위한 객체

## 8단계 - User Resource에서 GET 메서드 구현하기

## 9단계 - User Resource에서 POST 메서드 구현하기

## 10단계 - POST 메소드를 개선해 올바른 HTTP 상태 코드와 locat

- response status
    - Resource is not found → 404
    - Server exception → 500
    - Validation error → 400
    - Success → 200
    - Created → 201
    - No Content → 204
    - Unauthorized → 401
    - Bad Request → 400

## 11단계 - 예외 처리 구현하기 - 404 Resource Not found

## 12단계 - 모든 리소스를 대상으로 예외 처리 구현하기

- ControllerAdvice

## 13단계 - DELETE 메소드로 사용자 리소스 삭제하기

## 14단계 - REST API에서 유효성 검증하기

## 15단계 - 고급 REST API 기능의 개요

- Documentation
- Content Negotiation
- Internationalization - i18n
- Versioning
- HATEOAS
- Static Filtering
- Dynamic Filtering
- Monitoring
- …

## 16단계 - Open API 사양 및 Swagger 파악하기

- Swagger
- Open API

## 17단계 - Swagger 문서의 자동 생성 구성하기

- info
    - API 정보
- servers
    - API 노출 서버 위치
- paths
    - 노출될 모든 리소스에 대한 정보
- components
    - 스키마 정의

## 18단계 - 콘텐츠 협상 알아보기 - XML 지원 구현하기

- 콘텐츠 협상
    - Accept header

## 19단계 - REST API 의 국제화 알아보기

- Request Header
- Accept Language
    - en(English)
    - nl(Dutch)

## 20단계 - REST API 버전 관리 - URI 버전 관리

- API 버전 관리를 해야 함
    - URL
    - Request Parameter
    - Header
    - Media Type

## 21단계 - REST API 버전 관리 - 요청 매개변수, 헤더, 콘텐츠 협상

- GetMapping의 params사용하여 버전 관리 → 요청 매개변수
- GetMapping의 headers 사용해 버전 관리 → 헤더
- 미디어 타입: accept header
- 추천 방법?
    - URI Pollution: URI 버전 관리와 요청 매개변수 버전 관리의 경우 URL Pollution이 발생
    - Misuse of HTTP headers →
    - Caching
    - Can we execute the request on the browser?
    - API Documentation
    - ⇒ 완벽한 솔루션은 없음
- 기업에서는 일관된 버전 관리 방식을 사용해야 함

## 22단계 - REST API HATEOAS 구현하기

- HATEOAS : Hypermedia as the Engine of Application State
    - 데이터를 반환할 뿐만 아니라 후속작업을 알려줌

## 23단계 - REST API 정적 필터링 구현하기

- 직렬화: 객체를 스트림으로 변환
- JsonProperty
- Return only selected fields
    - Filtering

## 24단계 - REST API 동적 필터링 구현하기

- MappingJacksonValue

## 25단계 - Spring Boot Actuator로 API 모니터링하기

- Provides a number of endpoints
    - beans
    - health
    - metrics → 애플리케이션 실행 환경 및 커넥션 풀 정보
    - mappings…

## 26단계 - Spring Boot HAL Explorer로 API 탐색하기

- HAL : Json Hypertext Application Language

## 27단계 - JPA와 Hibernate를 이용해 REST API를 H2에 연결하기 - 개요

## 28단계 - User 엔티티 및 테스트 데이터 생성하기

## 29단계 - REST API를 개선하고 JPA와 Hibernate를 이용해 H2에 연결하기

## 30단계 - User 엔티티와 일대다 관계로 Post 엔티티 생성하기

- fetch

## 31단계 - 사용자의 모든 게시물을 가져올 GET API 구현하기

## 32단계 - 사용자에 대한 게시물을 생성할 POST API 구현하기

## 33단계 - JPA와 Hibernate가 만든 REST API 쿼리 살펴보기

## 34단계 - REST API를 MySQL 데이터베이스에 연결하기 - 개요

## 35단계 - REST API를 MySQL 데이터베이스에 연결하기 - 구현

## 36단계 - Spring Security로 기본 인증 구현하기

## 37단계 - Spring Security 기본 인증 설정 개선하기

- Spring Security
    - Filter Chains
        1. All requests should be authenticated
        2. If a request is not authenticated, a web page is shown
        3. CSRF → POST, PUT