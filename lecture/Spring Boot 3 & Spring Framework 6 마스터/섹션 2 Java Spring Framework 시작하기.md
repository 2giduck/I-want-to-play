# 섹션 2. Java Spring Framework 시작하기

## 1단계 - Java Spring Framework가 필요한 이유 이해하기

- Spring Framework
    - 쉽게 유지 가능한 애플리케이션 구축에 필요한 여러 중요한 기능 제공
- Spring Boot
    - Spring Framework 사용이 쉬워짐
- 두 개 다 생산성이 크게 높아짐

## 2단계 - Java Spring Framework 시작하기

- 목표
    - 스프링 프레임워크 핵심 기능 이해

## 3단계 - Maven과 Java로 새 Spring Framework 프로젝트 생성하기

## 4단계 - Java 게이밍 애플리케이션 시작하기

- GameRunner / Mario 클래스 만들기
- system.out은 좋은 사례가 아님, logging을 사용하는게 좋음

## 5단계 - 느슨한 결합과 강한 결합 알아보기

- 강한 결합이란?
    - 마리오 게임과 강하게 결합되어 있음
- 결합
    - 무언가를 변경하는데 얼마나 많은 작업이 관련되어 있는지에 대한 측정
    - ex. 엔진은 차에 결합되어 있음
    - ex. 타이어는 차에 느슨하게 결합되어 있음
    - ex. 노트북은 특정 공간에 느슨하게 결합되어 있음
- 가능한 코드를 적게 변경하면서 기능을 변경해야 함

## 6단계 - Java 인터페이스를 도입하여 느슨하게 결합된 앱 만들기

- 인터페이스
    - 특정 클래스에서 수행할 수 있는 공통된 작업을 나타냄

## 7단계 - Spring Framework를 도입하여 Java 앱 느슨하게 결합하기

- 객체는 JVM에서 생성됨
- 이걸 스프링 프레임워크가 모든 것을 관리하도록 변경

## 8단계 - 첫 번째 Java Spring Bean 및 Java Spring 설정 시작

- JVM 내부에서 스프링 컨텍스트를 생성하고 이름이 생성된 Bean을 만들고자 함

## 9단계 - Spring Java 설정 파일에서 더 많은 Java Spring Bean 만들기

- Record: 자바16부터 도입됨. 세터 게터 생성자 등을 만들 필요가 없음

## 10단계 - Spring Framework Java 구성 파일에서 자동 연결 구현

- Bean이름을 사용자 지정 가능
- 이름 대신 클래스로 빈을 찾을 수 있음
- 스프링에서 관리하는 기존 빈으로 새로운 빈을 만들 수 있음

## 11단계 - Spring Framework에 대한 질문 - 학습할 내용

- 스프링 컨테이너란?
- Java Bean과 Spring Bean 차이점
- Spring 프레임워크가 관리하는 Bean을 모두 나열하려면
- 여러 개의 Bean을 모두 사용할 수 있으면 어떻게 해야할까?
- 스프링이 객체를 만들기 위한 방법은?

## 12단계 - Spring IOC 컨테이너 살펴보기 - 애플리케이션 컨텍스트 및 Bean Factory

- 스프링 컨테이너(스프링 컨텍스트, IOC 컨테이너)
    - 스프링 빈과 수명주기를 관리함
- 빈 팩토리
- 애플리케이션 컨텍스트: 고급 스프링 컨테이너

## 13단계 - Java Bean, POJO, Spring Bean 살펴보기

- POJO
    - 오래된 자바 객체
- Java Bean : 세가지 제약을 준수하는 객체일 뿐
    - 인수 생성자가 없어야 함
    - 게터-세터가 있어야 함
    - Serializable을 구현해야 함
- Spring Bean
    - 스프링에서 관리하는 무엇이든

## 14단계 - Spring Framework Bean 자동 연결 살펴보기 - 기본 및 한정자

- Spring 프레임워크가 관리하는 Bean을 모두 나열하려면
    - context.getBeanDefinitionNames()
- 조건을 충족하는 빈이 여러개라면
    - Primary 지정
    - @Qualifier

## 15단계 - Spring Framework를 사용하여 Java 게이밍 앱의 Bean 관리

## 16단계 - Java Spring Framework에 대한 더 많은 질문 - 학습할 내용

- 스프링 빈이 모든 작업을 과연 쉽게 만들어주는가?

## 17단계 - Java Spring Framework 살펴보기 - 섹션 1 - 검토

## 퀴즈 1: Spring Framework 기본 - 퀴즈