# 섹션 7. Spring Framework, Hibernate로 Java 웹 애플리케이션 만들기

## 0단계 - Spring Boot를 이용한 웹 앱 제작 개요

## 1단계 - Spring initializer로 Spring Boot 웹 애플리케이션 만들기

## 2단계 - Spring Boot 프로젝트 간단히 살펴보기

## 3단계 - 첫 번째 Spring MVC 컨트롤러, @ResponseBody, @Controller

## 4단계 - 01 - HTML 응답을 제공하기 위해 Spring MVC 컨트롤러 개선하기

## 4단계 - 02 - 단계별로 알아보는 코딩 및 디버깅 과정

## 5단계 - Spring Boot Controller, @ResponseBody, 뷰를 이용하여 JSP로 리디렉션하기

- @ResponseBody를 붙이면 return을 바로 반환함
    - 그래서 뷰를 반환하고 싶으면 어노테이션을 제거해야 함

## 6단계 - 예제 - LoginController와 login 뷰 만들기

## 7단계 - 빠른 개요 - 웹의 작동 방식 - 요청과 응답

## 8단계 - RequestParam으로 쿼리 파라미터 잡기, 모델 소개

- jsp로 무언가를 전달할 때는 Model에 넣어서 전달
- jsp Expression Language(EL)

## 9단계 - 빠른 개요 - Spring Boot를 사용할 때 로깅의 중요성

## 10단계 - 디스패처 서블릿, 모델1, 모델2, 프론트 컨트롤러 알아보기

- Model1: JSP가 모든 것을 처리
- Model2: Model/View/Controller 분리
    - FrontController: 브라우저에서 오는 모든 요청은 하나의 프론트 컨트롤러에게 전달
    - DispatcherServlet이 프론트 컨트롤러 역할을 함

## 11단계 - 로그인 양식 만들기

## 12단계 - 모델로 JSP에 로그인 자격증명 표시하기

- @RequestMapping은 GET/POST 메소드 둘 다 처리함

## 13단계 - 하드코딩된 사용자 ID 및 패스워드 검증 추가하기

## 14단계 - Todo 기능 만들기 시작 - Todo와 TodoService 만들기

## 15단계 - Todo 리스트 페이지 처음 만들기

## 16단계 - 세션, 모델, 요청 이해하기 - @SessionAttributes

- @SessionAttributes를 사용하여 세션에 model을 넣을 수 있음
- Request Scope → 싱글 요청만 적용함
- Session Scope → 세션 정보가 다수의 요청에 걸쳐 적용됨

## 17단계 - Spring Boot 프로젝트에 JSTL을 추가하고 Todos를 테이블에 표시하기

## 18단계 - webjars를 사용하여 Bootstrap CSS 프레임워크를 Spring Boot 프로젝트에 추가하기

## 19단계 - Bootstrap CSS 프레임워크로 JSP 페이지 포맷 만들기

## 20단계 - Todo 추가하기 - 새로운 뷰 만들기

- redirect: {URL}

## 21단계 - Todo를 추가하기 위해 TodoService 개선하기

## 22단계 - Spring Boot Starter Validation을 이용하여 검증 추가하기

- front에서 required=”required” 추가하여 검증할 수 있음
    - 해커가 뛰어넘기 쉬움
- 검증
    1. spring-boot-starter-validation
    2. Command Bean
    3. Add Validations to Bean 
    4. Display Validation Errors in the View 

## 23단계 - 커맨드 빈으로 새 Todo 페이지 검증 구현하기

- 양방향 바인딩
    - 빈에서 뷰로

## 24단계 - Todo 삭제 기능 구현하기 - 새로운 뷰

## 25단계 - Todo 업데이트 구현하기 - 1 - Todo 업데이트 페이지 표시하기

## 26단계 - Todo 업데이트 구현하기 - 1 - Todo 변경사항 저장

## 27단계 - Todo 페이지에 목표 날짜 추가하기

## 28단계 - 내비게이션 바를 추가하고 JSP 프래그먼트 구현하기

## 29단계 - Spring Security 사용할 준비하기

## 30단계 - Spring Boot Starter Security로 Spring Security 설정하기

## 31단계 - 사용자 지정 유저와 패스워드 인코더를 이용하여 Spring Security 설정하기

## 32단계 - 하드코딩된 사용자 ID 삭제하고 리팩터링하기

## 33단계 - Todo 애플리케이션에 새로운 사용자 설정하기

## 34단계 - Spring Boot Starter Data JPA를 추가하고 H2 데이터베이스 준비하기

## 35단계 - H2 콘솔을 사용하기 위해 Spring Security 설정하기

## 36단계 - Todo 엔티티를 만들고 Todo 데이터를 H2에 채워넣기

## 37단계 - TodoRepository를 만들고 H2 데이터베이스와 list-todos 페이지 연결하기

## 38단계 - 01 - 모든 Todo 앱 기능을 H2 데이터베이스와 연결하기

## 38단계 - 02 - Spring Boot Starter JPA와 JpaRepository의 세부 작동방식 이해하기

- We added Data JPA and H2 dependencies
    - Spring Boot Auto Configuration does some magic

## 39단계 - Todo 앱을 MySQL 데이터베이스에 연결하기

## 40단계 - Docker 설치하기

## 41단계 - Todo 앱을 MySQL 데이터베이스에 연결하기