# 섹션 4. Spring Framework 고급 기능 살펴보기

## 1단계 - Spring Framework Beans의 지연 초기화와 즉시 초기화 알아보기

- default 초기화는 Eager 초기화
- Lazy → Bean을 지연하여 초기화 할지 결정
- Eager 초기화를 추천함
    - Spring 구성에 오류가 있을 경우 Eager라면 오류를 즉시 확인할 수 있음

## 2단계 - 지연 초기화와 즉시 초기화 비교하기

| Heading | Lazy  | Eager |
| --- | --- | --- |
| Initialization time | 처음 사용될 때 | 애플리케이션이 시작될 때 |
| Default | X | O |
| Code Snippet | @Lazy @Lazy(value=true) | @Lazy(value=false) |
| 초기화하다 오류 발생하면 | 런타임 예외 | 시작되지 않음 |
| 사용 | 별로 사용되지 않음 | 많이 사용됨 |
| 메모리 소비 | 적음 | 비교적 많음 |
| 추천도 | 빈이 아주 드믈게 사용될 때 | 대부분 |

## 3단계 - Java Spring Framework Bean 스코프 - 프로토타입 및 싱글톤

- Prototype → 빈을 호출할 때마다 매번 다른 인스턴스를 생성하고 싶을 때
- 싱글톤 → IoC 컨테이너 당 오브젝트가 단 하나
- Request Scope - HTTP 요청 당 객체 인스턴스가 하나 생성됨
- Session Scope - Session 당 하나
- Application - Application 당 하나

## 4단계 - 프로토타입과 싱글톤 비교하기 - Spring Framework Bean 스코프

| Heading | Prototype | Singleton |
| --- | --- | --- |
| Instances | IOC 컨테이너 마다 많이 생성 가능 | IOC 컨테이너 당 하나  |
| Beans | 빈을 참조할 때마다 새 인스턴스 생성 | 기존의 인스턴스가 사용됨 |
| Default | X | O |
| Code Snippet | @Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE) |  |
| Usage | Rarely used | Very frequently |
| Recommended | Stateful beans | Stateless beans |

## 5단계 - Spring Bean 알아보기 - PostConstruct 및 PreDestroy

## 6단계 - Jakarta EE의 발전 - J2EE 및 Java EE와 비교

- 초기에는 모든 기능이 JDK에 있었음
- J2EE - Java 2 Platform Enterprise Edition
- Java EE - Java Platform Enterprise Edition
- Jakarta EE
    - JSP
    - Java Standard Tag Library
    - Jakarta Enterprise Beans
    - Jakarta RESTful Web Services
    - Jakarta Bean Validation
    - Jakarta Contexts and Dependency Injection
    - Jakarta Persistence

## 7단계 - Spring Framework 및 Java를 통해 Jakarta CDI 알아보기

- CDI는 specification(인터페이스)
- Important Inject API Annotations
    - Inject
    - Named
    - Qualifier…

## 8단계 - Java Spring XML 설정 알아보기

## 9단계 - Java 어노테이션과 XML 설정 알아보기 - Java Spring Framework

## 10단계 - Spring Framework 스테레오타입 어노테이션 - Component 등

- 최대한 구체적인 어노테이션 사용

## 11단계 - 간단한 복습 - 중요한 Spring Framework 어노테이션

## 12단계 - 간단한 복습 - 중요한 Spring Framework 개념

## 13단계 - Spring 전체 구조 알아보기 - Framework, 모듈, 프로젝트