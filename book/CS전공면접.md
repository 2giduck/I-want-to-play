## 면접을 위한 CS 전공지식 노트
- 책에는 JS 예제도 나와있지만, Java 예제만 정리
- 또는, 예제 간략화 진행

### Chapter 1. 디자인 패턴과 프로그래밍 패러다임
#### Section 1. 디자인 패턴 
- 디자인 패턴: 프로그램을 설계할 때 발생했던 문제점들을 객체 간의 상호관계 등을 이용하여 해결할 수 있도록 하나의 '규약' 형태로 만들어 놓은 것
###### 싱글톤 패턴 
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

###### 팩토리 패턴 
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

###### 전략 패턴
- 정책 패턴
- 객체의 행위를 바꾸고 싶은 경우 '직접' 수정하지 않고 전략이라고 부르는 '캡슐화한 알고리즘'을 컨텍스트 안에서 바꿔주면서
상호 교체가 가능하게 만드는 패턴

#### Section 2. 프로그래밍 패러다임 