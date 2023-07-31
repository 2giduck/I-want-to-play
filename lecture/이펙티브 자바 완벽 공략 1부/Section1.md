### Section 1. 생성자 대신 정적 팩터리 메서드를 고려하라
#### 핵심 정리
- 정적 팩터리 메서드의 장점
  - 생성자의 시그니쳐가 중복되는 경우에 사용하면 좋음 
    ```java 
    public static Order primeOrder(Product product) {
        Order order = new Order();
        order.prime =  true;
        order.product = product;
        return order;
    }
    ```
  - 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.
    ```java
    public class Settings {
        private static final Settings SETTINGS = new Settings();
    
        public static Settings newInstance() {
            return SETTINGS;
        }
    }
    ```
  - 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.
    - java8 부터는 인터페이스에도 static 메서드를 선언할 수 있다.
  - 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.
  - 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.
  ```java
  ServiceLoader<HelloService> loader = ServiceLoader.load(HelloService.class);
  Optional<HelloService> helloServiceOptional = loader.findFirst();
  helloServiceOptional.ifPresent(h -> {
    System.out.println(h.hello());
  })  
  ```
- 정적 팩터리 메서드의 단점
  - 상속을 허용하지 않는다
    - 생성자가 private이기 때문 
  - 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.


#### 완벽 공략
- 열거 타입은 인스턴트가 하나만 만들어짐을 보장한다
  - 타입-세이프티 보장 
  - '=='쓰는 것을 권장
  - EnumSet, EnumMap 사용 가능 

- 같은 객체가 자주 요청되는 상황이라면 플라이웨이트 패턴을 사용할 수 있다.
  - 플라이웨이트(Flyweight): 객체를 가볍게 만들어 메모리 사용을 줄이는 패턴

- 인터페이스가 정적 메서드를 가질 수 없다는 제한이 풀렸기 때문에 인스턴스화 불가 동반 클래스를 둘 이유가 별로 없다.
  - 인터페이스에 메서드 정의하려면 default / static 사용

- 서비스 제공자 프레임워크를 만드는 근간이 된다.

- 서비스 제공자 인터페이스가 없다면 각 구현체를 인스턴스로 만들 때 리플렉션을 사용해야 한다.
  - 클래스로더를 통해 읽어온 클래스 정보를 사용하는 기술