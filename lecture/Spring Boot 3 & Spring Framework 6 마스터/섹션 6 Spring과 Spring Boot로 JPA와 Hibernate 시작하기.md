# 섹션 6. Spring과 Spring Boot로 JPA와 Hibernate 시작하기

## 1단계 - JPA와 Hibernate 시작하기 - 목표

## 2단계 - JPA와 Hibernate에 맞는 새 Spring Boot 프로젝트 설정하기

## 3단계 - H2 콘솔 실행하기 및 H2에서 과정 테이블 생성하기

- localhost:8080/h2-console

## 4단계 - Spring JDBC 실행하기

## 5단계 - Spring JDBC를 사용하여 하드코드로 작성된 데이터 삽입하기

- CommandLineRunner를 구현한 클래스를 작성하면 애플리케이션 시작 시에 로직 수행 가능

## 6단계 - Spring JDBC를 사용하여 데이터 삽입 및 삭제하기

## 7단계 - Spring JDBC를 사용하여 데이터 쿼리하기

## 8단계 - JPA와 EntityManager 시작하기

## 9단계 - JPA의 마법 살펴보기

- JDBC
    - 많은 SQL 쿼리 작성
    - 자바 코드도 많이 써야함
- Spring JDBC
    - 많은 SQL 쿼리 작성
    - 자바 코드는 적게 씀
- JPA
    - query 작성하지 않음
    - 테이블과 엔티티를 연결

## 10단계 - Spring Data JPA 시작하기

- Spring Data JPA
    - JPA를 더 간단하게 사용함

## 11단계 - Spring Data JPA 특징 살펴보기

## 12단계 - Hibernate와 JPA 차이 이해하기

- JPA
    - JPA는 인터페이스 API
    - 객체를 테이블로 매핑하는 방식을 정의
    - ex. 엔티티를 매핑하는 방법, 속성을 매핑하는 방법 등을 정의함
- Hibernate
    - JPA에서 유명한 구현체
    - JPA를 구현함