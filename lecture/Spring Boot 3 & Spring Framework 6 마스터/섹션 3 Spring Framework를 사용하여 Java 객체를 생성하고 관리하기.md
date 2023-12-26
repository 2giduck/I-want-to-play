# 섹션 3. Spring Framework를 사용하여 Java 객체를 생성하고 관리하기

## 1단계 - Java 객체의 생성 및 관리를 위한 Spring Framework 이해하기

- Component → 스프링이 관리할 컴포넌트
- ComponentScan을 이용해 어떤 패키지를 스캔할 것인지 적어주어야 함

## 2단계 - Spring 컴포넌트에 대한 Primary 및 Qualifier 어노테이션 알아보기

- Primary
- Qualifier

## 3단계 - Primary와 Qualifier - 어떤 Spring 어노테이션을 사용할까요?

- Primary - 한 빈에게 우선권 주기
- Qualifier - 특정하게 지정된 Bean을 자동 와이어링

## 4단계 - Spring Framework 알아보기 - 의존성 주입의 다양한 유형

- 생성자 기반 → Autowired가 의무가 아님
- setter 기반
- 필드 기반 : 생성자나 수정자가 없음.

→ 스프링은 생성자 기반을 추천함: 모든 초기화가 하나의 메소드에서 발생함 

## 5단계 - Java Spring Framework - 중요한 용어 이해하기

- Component Scan: 스프링 프레임워크가 컴포넌트 클래스 위치를 찾게 해줌
- 의존성 주입: 빈을 주입

## 6단계 - Java Spring Framework - @Component와 @Bean 비교하기

|  | Component | Bean |
| --- | --- | --- |
| 어디서 | 어떤 자바 클래스에서도 사용할 수 있음 | 스프링 Configuration 클래스에서만 사용 가능  |
| 사용하기 쉬움 | 아주 쉬움 | 모든 코드를 써야 함 |
| Autowiring | 모든 주입 방법을 쓸 수 있음 | 메소드 호출을 통해 쓸 수 있음  |
| 누가 빈을 생성하나 | 스프링 프레임워크 | 당신 |
| 권장 방법 | 대부분의 경우 | - 생성 전에 비즈니스 로직을 실행해야 하는 경우
- 3rd party library  |

## 7단계 - Java Spring 애플리케이션에 의존성이 있는 이유가 무엇일까요?

## 8단계 - 예제: Real World Java Spring Framework Example의 솔루션

## 9단계 - Java와 함께 Spring Framework 알아보기 - 섹션 2 - 복습