### Section 2. 생성자에 매개변수가 많다면 빌더를 고려하라 
#### 핵심 정리
###### 생성자 체이닝과 자바빈즈 
- 생성자 체이닝의 단점
  - 매개변수가 늘어나면 코드를 읽기 어려움 (IDE 기능을 사용하지 않는다면)
- 자바빈즈: getter와 setter 정해놓은 표준 패턴

#### 완벽 공략
###### 자바 빈
- 자바 빈: java.beans 패키지 안에 있는 모든 것
  - 아규먼트 없는 기본 생성자
  - getter와 setter 메서드 이름 규약
  - serializable 인터페이스 구현 
  
###### 객체 프리징
- 'freeze'를 사용해 객체 프리징

###### 빌더 패턴
- 동일한 프로세스를 거쳐 다양한 구성의 인스턴스를 만드는 방법

###### IllegalArgumentException
- IllegalArgumentException
  - 어떤 argument가 잘못되었는지 알려주기 
- 메서드 선언부에 throw하는 이유 
  - 어떤 에러가 발생하는지 명시적으로 알려줄 때
- checked exception을 사용하는 이유
  - 그 에러가 발생했을 때 클라이언트가 후속 작업을 해주길 바라는 경우

###### 가변 인수 
- 한 메서드에 가변 인수는 한개만 선언 가능, 맨 뒤에만 선언 가능
```java
public void printNumbers(int... numbers) {
    
        }
```