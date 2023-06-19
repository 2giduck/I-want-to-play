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
###### 1.1.6 이터레이터 패턴
###### 1.1.7 노출모듈 패턴
###### 1.1.8 MVC 패턴
###### 1.1.9 MVP 패턴
###### 1.1.10 MVVM 패턴

#### Section 1.2 프로그래밍 패러다임
###### 1.2.1 선언형과 함수형 프로그래밍
###### 1.2.2 객체지향 프로그래밍
###### 1.2.3 절차형 프로그래밍
###### 1.2.4 패러다임의 혼합