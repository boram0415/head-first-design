# 헤드 퍼스트 디자인 패턴 - Chapter 3 요약 및 정리

## ☕ 데코레이터 패턴의 세계로 떠나기

이번 챕터에서는 **데코레이터 패턴**(**Decorator Pattern**)을 배웠다.

우리는 **객체의 기능을 동적으로 확장**하고 싶을 때가 많다. 하지만 상속을 사용하면 유연성이 떨어지고 코드가 복잡해질 수 있다. 이를 해결하기 위해 **데코레이터 패턴을 사용**할 수 있다.

예제로 **커피 주문 시스템**을 만들어보면서 데코레이터 패턴을 이해해보자.

---

## ☕ 1. 커피 주문 시스템 (Beverage)

커피 주문 시스템을 설계한다고 가정한다.
기본적인 커피(Beverage) 클래스가 있고, 다양한 커피 종류(Espresso, HouseBlend 등)를 추가할 수 있다.

### 💡 기본 설계 (Beverage 클래스)

```java
public abstract class Beverage {
    String description = "Unknown Beverage";
    
    public String getDescription() {
        return description;
    }
    
    public abstract double cost();
}
```

그리고 커피 종류를 정의한다.

```java
public class Espresso extends Beverage {
    public Espresso() {
        description = "Espresso";
    }
    
    public double cost() {
        return 1.99;
    }
}

public class HouseBlend extends Beverage {
    public HouseBlend() {
        description = "House Blend Coffee";
    }
    
    public double cost() {
        return 0.89;
    }
}
```

이제 `Espresso`나 `HouseBlend` 객체를 생성하여 가격을 계산할 수 있다.

---

## 🚀 2. 문제 발생: 새로운 추가 기능 (우유, 모카, 휘핑크림)

이제 커피에 **우유, 모카, 휘핑크림** 등을 추가하는 기능을 만들려고 한다면?

1. **상속을 사용하면 문제 발생**
   - `MochaEspresso`, `WhipEspresso`, `SoyHouseBlend`, `MochaSoyWhipEspresso` 등 다양한 조합이 필요
   - 모든 조합을 만들려면 **클래스 개수가 폭발적으로 증가**함

💥 잘못된 확장 예시

```java
public class MochaEspresso extends Espresso {
    public double cost() {
        return super.cost() + 0.20;
    }
}
```

이 방식은 유연하지 않기 때문에 **데코레이터 패턴을 적용**해 보자.

---

## 🔥 3. 데코레이터 패턴 적용하기

**데코레이터 패턴**은 **상속이 아니라 조합(Composition)을 이용하여 기능을 추가**하는 패턴.

이를 위해 **추가 기능**(**CondimentDecorator**)을 따로 분리하여 기존 객체를 감싸는 방식으로 구현.

### 데코레이터 패턴을 적용한 설계

```java
public abstract class CondimentDecorator extends Beverage {
    public abstract String getDescription();
}
```

### 우유, 모카, 휘핑크림 추가하기

```java
public class Mocha extends CondimentDecorator {
    Beverage beverage;
    
    public Mocha(Beverage beverage) {
        this.beverage = beverage;
    }
    
    public String getDescription() {
        return beverage.getDescription() + ", Mocha";
    }
    
    public double cost() {
        return beverage.cost() + 0.20;
    }
}
```

위와 같은 방식으로 **Mocha, Whip, Soy** 등을 구현할 수 있다.

```java
public class Whip extends CondimentDecorator {
    Beverage beverage;
    
    public Whip(Beverage beverage) {
        this.beverage = beverage;
    }
    
    public String getDescription() {
        return beverage.getDescription() + ", Whip";
    }
    
    public double cost() {
        return beverage.cost() + 0.10;
    }
}
```

이제 다양한 조합을 쉽게 만들 수 있다! 🎉

---

## 🎯 데코레이터 패턴을 활용한 커피 주문 예제

```java
public class StarbuzzCoffee {
    public static void main(String args[]) {
        Beverage beverage = new Espresso();
        System.out.println(beverage.getDescription() + " $" + beverage.cost());
        
        Beverage beverage2 = new HouseBlend();
        beverage2 = new Mocha(beverage2);
        beverage2 = new Whip(beverage2);
        System.out.println(beverage2.getDescription() + " $" + beverage2.cost());
    }
}
```

✅ **출력 결과**
```
Espresso $1.99
House Blend Coffee, Mocha, Whip $1.19
```

이제 **새로운 추가 기능을 쉽게 추가할 수 있다!**

---

## 🎯 데코레이터 패턴의 장점

✅ **유연성이 뛰어남** → 새로운 기능을 추가할 때 기존 클래스를 수정할 필요가 없음  
✅ **클래스 폭발 문제 해결** → 모든 조합을 만들지 않아도 됨  
✅ **런타임에 동적으로 기능 추가 가능** → 객체를 감싸면서 기능을 변경 가능  

---

## 🔥 정리

- **데코레이터 패턴**은 객체에 **기능을 동적으로 추가**할 수 있는 패턴이다.
- **상속을 사용하지 않고 조합(Composition)을 활용**하여 유연성을 높인다.
- **예제: 커피 주문 시스템**을 데코레이터 패턴을 이용하여 확장 가능한 방식으로 개선할 수 있다.

데코레이터 패턴을 이해하면 **스트림, 필터, GUI 위젯 시스템 등 다양한 곳에서 활용**할 수 있다.
