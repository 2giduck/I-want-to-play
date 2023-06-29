## 면접을 위한 CS 전공지식 노트
- 책에는 JS 예제도 나와있지만, Java 예제만 정리
- 또는, 예제 간략화 진행

### Chapter 1. 디자인 패턴과 프로그래밍 패러다임
#### Section 1.1 디자인 패턴 
- 디자인 패턴: 프로그램을 설계할 때 발생했던 문제점들을 객체 간의 상호관계 등을 이용하여 해결할 수 있도록 하나의 '규약' 형태로 만들어 놓은 것
###### 1.1.1 싱글톤 패턴 
- 하나의 클래스에 오직 하나의 인스턴스만 가지는 패턴 
- 보통 데이터베이스 연결 모듈에 많이 사용함
```
class Singleton {
    private static class singleInstanceHolder {
        private static final Singleton INSTANCE = new Singleton();
    }
    public static Singleton getInstance() {
        return singleInstanceHolder.INSTANCE;
    }
}    
```
|장점|단점|
  |------|---|
|인스턴스 생성 시 드는 비용이 줄어듬|의존성이 높아짐 - TDD에 걸림돌이 됨|

###### 1.1.2 팩토리 패턴 
- 객체를 사용하는 코드에서 객체 생성 부분을 떼어내 추상화
```
abstract class Coffee {
    public abstract int getPrice();
    
    @Override
    public String toString() {
        return "Hi this coffee is " + this.getPrice();
    }
}
class CoffeeFactory {
    public static Coffee getCoffee(String type, int price) {
        if ("Latte".equalsIgnoreCase(type)) return new Latte(price);
        else return new DefaultCoffee();
   }
}
class DefaultCoffee extends Coffee {
    private int price;
    
    public DefaultCoffee() {
        this.price = -1;
    }
    
    @Override
    public int getPrice() {
        return this.price;
    }
}
class Latte extends Coffee {
    private int price;
    
    public Latte(int price) {
        this.price = price;
    }
    
    @Override
    public int getPrice() {
        return this.price;
    }
}
```

###### 1.1.3 전략 패턴
- 정책 패턴
- 객체의 행위를 바꾸고 싶은 경우 '직접' 수정하지 않고 전략이라고 부르는 '캡슐화한 알고리즘'을 컨텍스트 안에서 바꿔주면서
상호 교체가 가능하게 만드는 패턴
```
interface PaymentStrategy {
    public void pay(int amount);
}

class KAKAOCardStrategy implements PaymentStrategy {
    private String name;
    private String cardNumber;
    private String cvv;
    private String dateOfExpiry;
    
    public KAKAOCardStrategy(String nm, String ccNum, String cvv, String expiryDate) {
        this.name = nm;
        this.cardNumber = ccNum;
        this.cvv = cvv;
        this.dateOfExpiry = expiryDate;
    }
    
    @Override
    public void pay(int amount) {
        System.out.println(amount + " paid using KAKAOCard.");
    }
}

class LUNACardStrategy implements PaymentStrategy {
    private String emailId;
    private String password;
    
    public LUNACardStrategy(String email, String pwd) {
        this.emailId = email;
        this.password = pwd;
    }
    
    @Override
    public void pay(int amount) {
        System.out.println(amount + " paid using LUNACard.");
    }
}
...(생략)
```

###### 1.1.4 옵저버 패턴
- 주체가 어떤 객체의 상태 변화를 관찰하다가 상태 변화가 있을 때마다 옵저버들에게 변화를 알려주는 패턴
- 주체: 객체의 상태 변화를 보고 있는 관찰자
- 옵저버: 객체의 상태 변화에 따라 변화 사항이 생기는 객체들
- 주로 이벤트 기반 시스템에 사용됨, MVC 패턴 
```
interface Subject {
    public void register(Observer obj);
    public void unregister(Observer obj);
    public void notifyObservers();
    public Object getUpdate(Observer obj);
}

interface Observer {
    public void update();
}

class Topic implements Subject {    // 주체이자 객체
    private List<Observer> observers;
    private String message;
    
    public Topic() {
        this.observers = new ArrayList<>();
        this.message = "";
    }
    
    @Override
    public void register(Observer obj) {
        if (!observers.contains(obj)) observers.add(obj);
    }
    
    @Override
    public void unregister(Observer obj) {
        observers.remove(obj);
    }
    
    @Override
    public void notifyObservers() {
        this.observers.forEach(Observer::update);
    }
    
    @Override
    public Object getUpdate(Observer obj) {
        return this.message;
    }
    
    public void postMessage(String msg) {
        System.out.println("Message sended to Topic: " + msg);
        this.message = msg;
        notifyObservers();
    }
}

class TopicSubscriber implements Observer {
    private String name;
    private Subject topic;
    
    public TopicSubscriber(String name, Subject topic) {
        this.name = name;
        this.topic = topic;
    }
    
    @Override
    public void update() {
        String msg = (String) topic.getUpdate(this);
        System.out.println(name + ":: got message >> " + msg);
    }
}
...(생략)
```
###### 1.1.5 프록시 패턴과 프록시 서버
- 프록시 패턴: 대상 객체에 접근하기 전 그 접근에 대한 흐름을 가로채 대상 객체 앞단의 인터페이스 역할을 함 
  - 객체의 속성, 변환을 보완
  - 보안, 데이터 검증, 캐싱, 로깅에 사용

- 프록시 서버: 서버와 클라이언트 사이에서 클라이언트가 자신을 통해 다른 네트워크 서비스에 간접적으로 접속할 수 있게 해주는 프로그램 
  - nginx: Node.js 서버 앞단의 프록시 서버
  - CloudFlare

- CDN(Content Delivery Network): 사용자가 인터넷에 접속하는 곳과 가까운 곳에서 콘텐츠를 캐싱 또는 배포하는 서버 네트워크 

###### 1.1.6 이터레이터 패턴
- 이터레이터 패턴: 이터레이터를 사용하여 컬렉션의 요소들에 접근하는 디자인 패턴 
  - 자료형의 구조와는 상관 없이 이터레이터라는 하나의 인터페이스로 순회 가능

###### 1.1.7 노출모듈 패턴
- 노출모듈 패턴: 즉시 실행 함수를 통해 private, public 같은 접근 제어자를 만드는 패턴 
```
const pukaba = (() => {
    const a = 1
    const b = () => 2
    const public = {
        c: 2,
        d: () => 3
    }
    return public
})()
console.log(pukuba) // {c:2, d: [Function: d]}
console.log(pukuba.a) // undefined
```

###### 1.1.8 MVC 패턴
- MVC 패턴: Model, View, Controller로 이루어진 패턴 
  - 애플리케이션의 구성 요소를 세 가지 역할로 구분하여 개발 프로세스에서 각각의 구성 요소에만 집중하여 개발 
  - 재사용성과 확장성이 용이함 
  - 애플리케이션이 복잡해질수록 모델과 뷰의 관계가 복잡해짐 
  - Spring 

###### 1.1.9 MVP 패턴
- MVP 패턴: MVC에서 C에 해당하는 컨트롤러가 프레젠터(presenter)로 교체된 패턴
  - View와 프레젠터는 1:1 관계 

###### 1.1.10 MVVM 패턴
- MVVM 패턴: C에 해당하는 컨트롤러가 뷰 모델(View Model)로 변경된 패턴 
  - 뷰 모델: 뷰를 더 추상화한 계층, 커맨드와 데이터 바인딩을 가짐 
  - Vue.js
  
#### Section 1.2 프로그래밍 패러다임
- 프로그래밍 패러다임: 프로그래머에게 프로그래밍의 관점을 갖게 해주는 역할을 하는 개발 방법론 
###### 1.2.1 선언형과 함수형 프로그래밍
- 선언형 프로그래밍: '무엇을' 풀어내는가에 집중하는 패러다임 
  - 프로그램은 함수로 이루어진 것이다.
  - 함수형 프로그래밍(javascript): '순수 함수'들을 블록처럼 쌓아 로직을 구현하고, '고차 함수'를 통해 재사용성을 높임
```javascript
const list = [1, 2, 3, 4, 5, 11, 12]
const ret = list.reduce((max, num) => num > max ? num : max, 0)
console.log(ret)
```

- 순수 함수: 출력이 입력에만 의존하는 함수
- 고차 함수: 함수가 함수를 값처럼 매개변수로 받아 로직을 생성할 수 있는 것 

###### 1.2.2 객체지향 프로그래밍
- 객체지향 프로그래밍: 객체들의 집합으로 프로그램의 상호 작용을 표현하며 데이터를 객체로 취급하여 객체 내부에 선언된 메서드를 활용하는 방식
  - 설계에 많은 시간이 소요됨
  - 처리 속도가 다른 프로그래밍 패러다임에 비해 상대적으로 느림 
```javascript
const ret = [1, 2, 3, 4, 5, 11, 12]
class List {
    constructor(list) {
        this.list = list;
        this.mx = list.reduce((max, num) => num > max ? num : max, 0)
    }
    getMax() {
        return this.mx;
    }
}
const a = new List(ret)
console.log(a.getMax())
```

- 객체지향 프로그래밍의 특징
  - 추상화: 복잡한 시스템으로부터 핵심적인 개념을 간추려내는 것
  - 캡슐화: 객체의 속성과 메서드를 하나로 묶고 일부를 외부에 감추어 은닉
  - 상속성: 상위 클래스의 특성을 하위 클래스가 이어받아 재사용하거나 추가, 확장
  - 다형성: 하나의 메서드나 클래스가 다양한 방법으로 동작, 오버로딩&오버라이딩
    - 오버로딩: 같은 이름을 가진 메서드를 여러 개 두는 것, 컴파일 중에 발생하는 '정적' 다형성
    - 오버라이딩: 상위 클래스로부터 상속받은 메서드를 하위 클래스가 재정의, 런타임 중에 발생하는 '동적' 다형성 

- 설계 원칙(SOLID)
  - 단일 책임 원칙(SRP; Single Responsibility Principle): 모든 클래스는 각각 하나의 책임만 가져야 함
  - 개방-폐쇄 원칙(OCP; Open Closed Principle): 기존의 코드는 잘 변경하지 않으면서 확장은 쉽게 할 수 있어야 함
  - 리스코프 치환 원칙(LSP; Liskov Substitution Principle): 객체는 하위 타입의 인스턴스로 바꿀 수 있어야 함
  - 인터페이스 분리 원칙(ISP, Interface Segregation Principle): 하나의 일반적인 인터페이스보다 구체적인 여러 개의 인터페이스를 만들어야 함 
  - 의존 역전 원칙(DIP, Dependency Inversion Principle): 변하지 않는 것에 의존하는 원칙

###### 1.2.3 절차형 프로그래밍
- 절차형 프로그래밍: 로직이 수행되어야 할 연속적인 계산 과정으로 이루어짐
  - 코드의 가독성이 좋으며 실행 속도가 빠름 
  - 계산이 많은 작업에 쓰임 
  - 모듈화가 어렵고 유지 보수성이 떨어짐
```javascript
const ret = [1, 2, 3, 4, 5, 11, 12]
let a = 0
for (let i = 0; i< ret.length; i++) {
    a = Math.max(ret[i], a)
}
console.log(a)
```

###### 1.2.4 패러다임의 혼합
- 어떤 패러다임이 가장 좋은지에 대한 정답은 없음
- 비즈니스 로직이나 서비스의 특성을 고려하여 패러다임 정하기 
- 여러 패러다임을 조합하여 상황과 맥락에 따라 패러다임 간의 장점만 취해 개발하기 