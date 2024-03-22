# 섹션 17. Spring Boot와 함께 하는 Spring AOP 배우기

최종 편집 일시: 2024년 3월 22일 오후 10:13
즐겨찾기: No
상태: inbox

## 1단계 - Spring AOP 시작하기 - 개요

## 2단계 - 관점 지향 프로그래밍이란

- 계층적 지향
    - 웹: 뷰 로직
    - 비즈니스: 비즈니스 로직
    - 데이터 : 영속성 로직
- 공통요소
    - 시큐리티
    - 성능
    - 로깅
- AOP 프레임워크
    - Spring AOP
    - AspectJ

## 3단계 - Spring AOP를 이용한 Spring Boot 프로젝트 생성하기

## 4단계 - Spring AOP에 필요한 Spring 컴포넌트 만들기

- CommandLineRunner

## 5단계 - AOP 로깅 애스펙트와 포인트컷 만들기

## 6단계 - AOP 용어 훑어보기

- Advice: 어떤 코드를 실행할건지?
- Pointcut: 어떤 메서드를 인터셉트할 것인가
- Aspect: Advice와 Pointcut의 조합
- Weaver: AOP를 구현한 클래스
- JoinPoint: Pointcut이 참이면 advice가 실행됨 Advice 실행 인스턴스

## 7단계 - AOP 어노테이션 @After, @AfterReturning, @AfterThrowing

- After: 메소드가 수행됨 뒤 수행할 작업
- AfterReturning: 메소드가 성공적으로 수행되었다면 수행할 작업
- AfterThrowing: 메소드가 예외를 던질 때

## 8단계 - Timer 클래스와 함께 Around AOP 어노테이션 배우기

- Around: 메소드 실행 주변에 뭔가를 함

## 9단계 - 베스트 프랙티스 - 공용 포인트컷 정의하기

## 10단계 - TrackTime 어노테이션 만들어 보기

## 11단계 - Spring AOP 시작하기 - 마무리