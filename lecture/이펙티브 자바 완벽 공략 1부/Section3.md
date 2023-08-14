### Section 3. 생성자나 열거 타입으로 싱글턴임을 보증하라.
#### 핵심 정리
###### 생성자를 사용하는 방법 
- private 생성자 + public static final 필드
```java
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    
    private Elvis() {}
}
```
- 장점
  - 간결하고 싱글턴임을 API에 들어낼 수 있음
- 단점
  - 싱글톤을 사용하는 클라이언트 테스트를 하기 어려워짐 -> 인터페이스로 해결 가능!
  - 리플렉션으로 private 생성자를 호출할 수 있음 -> 생성자에 로직 넣기 (but 간결하지 않음)
    ```java 
    private Elvis() {
      if (created) {
        throw new UnsupportedOperationException("can't be created by constructor.");
      }
      created= true;
    }
    ```
  - 역직렬화 할 때 새로운 인스턴스가 생길 수 있음

###### 생성자를 사용하는 방법 2
- private 생성자 + 정적 팩터리 메서드
```java
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() {}
    public static Elvis getInstance() {return INSTANCE;};
}
```
- 장점
  - API를 바꾸지 않고도 싱클턴이 아니게 변경할 수 있음
  - 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있음
  - 정적 팩터리의 메서드 참조를 공급자(Supplier)로 사용할 수 있음

###### 열거 타입
- 가장 간단한 방법이며 직렬화와 리플렉션에도 안전함
- 대부분의 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법
```java
public enum Elvis {
    INSTANCE;
    
    private Elvis() {}
  
    public void leaveTheBuilding() { System.out.println("기다려 자기야, 지금 나갈게!"); }
    
    public static void main(String[] args) {
        Elvis elvis = Elvis.INTANCE;
        elvis.leaveTheBuilding();
    }
}
```

#### 완벽 공략
###### 메서드 참조 
- 메소드 하나만 호출하는 람다 표현식을 줄여쓰는 방법
```java
people.sort((p1, p2) -> Person.compareByAge(p1, p2));

people.sort(Person::comparaeByAge);
```
###### 함수형 인터페이스
- 람다 표현식과 메소드 참조에 대한 "타겟 타입"을 제공함
- 타겟 타입은 변수 할당, 메소드 호출, 타입 변환에 활용할 수 있음
- 자바에서 제공하는 기본 함수형 인터페이스 익혀둘 것! -> Function, Supplier, Consumer, Predicate
- 함수형 인터페이스를 만드는 방법

###### 객체 직렬화 
- 객체를 바이스트림으로 상호 변환하는 기술
- 바이트스트림으로 변환한 객체를 파일로 저장하거나 네트워크를 통해 다른 시스템으로 전송할 수 있음
- Serializable 인터페이스 구현
- transient를 사용해서 직렬화 하지 않을 필드 선언하기
- serialVersionUID는 언제 왜 사용하는가?
- 심화 학습) 객체 직렬화 스펙


