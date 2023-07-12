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