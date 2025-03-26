# 헤드 퍼스트 디자인 패턴 - Chapter 8 요약 및 정리

## 🧩 템플릿 메소드 패턴: 알고리즘의 뼈대를 정의하고, 세부 구현은 서브클래스에 맡기자!

이번 챕터에서는 **템플릿 메소드 패턴**(**Template Method Pattern**)을 배웠다.

템플릿 메소드 패턴은 **알고리즘의 구조(템플릿)를 상위 클래스에서 정의하고, 세부 구현은 하위 클래스에서 결정하는 패턴**이다.

즉, **일정한 절차를 따르되, 세부적인 내용은 상황에 맞게 유연하게 바꿀 수 있는 구조를 만든다.**

---

## ☕ 1. 템플릿 메소드 패턴 이해하기

우리는 **차와 커피를 끓이는 클래스**를 만들려고 한다. 두 음료 모두 비슷한 절차를 따르지만, 약간의 차이가 있다.

- 물을 끓인다
- 차 or 커피를 우린다
- 컵에 따른다
- 레몬 or 설탕을 넣는다

### 😣 문제점
만약 절차가 유사하다고 해서 두 클래스의 코드를 복붙하거나, 공통 메서드만 따로 빼서 만들면 **코드 중복 또는 제어 흐름의 불일치**가 발생할 수 있다.

---

## 🏗 2. 템플릿 메소드 패턴 구조

### 🎯 상위 클래스에서 알고리즘 뼈대 정의
```java
public abstract class CaffeineBeverage {
    // 템플릿 메소드: 알고리즘의 순서를 정의
    public final void prepareRecipe() {
        boilWater();
        brew();
        pourInCup();
        if (customerWantsCondiments()) {
            addCondiments();
        }
    }

    abstract void brew();
    abstract void addCondiments();

    void boilWater() {
        System.out.println("Boiling water");
    }

    void pourInCup() {
        System.out.println("Pouring into cup");
    }

    // Hook 메서드 (선택적으로 오버라이드 가능)
    boolean customerWantsCondiments() {
        return true;
    }
}
```

- `prepareRecipe()`가 템플릿 메소드이며, **변하지 않는 알고리즘의 흐름을 정의**한다.
- `brew()`와 `addCondiments()`는 **하위 클래스가 구체적으로 구현**해야 하는 부분이다.
- `customerWantsCondiments()`는 **훅(hook) 메서드**로, 필요하면 오버라이드할 수 있다.

---

## 🍵 3. 서브클래스 구현하기: 차(Tea)와 커피(Coffee)

### 💡 Tea 클래스
```java
public class Tea extends CaffeineBeverage {
    @Override
    void brew() {
        System.out.println("Steeping the tea");
    }

    @Override
    void addCondiments() {
        System.out.println("Adding lemon");
    }
}
```

### 💡 Coffee 클래스
```java
public class Coffee extends CaffeineBeverage {
    @Override
    void brew() {
        System.out.println("Dripping coffee through filter");
    }

    @Override
    void addCondiments() {
        System.out.println("Adding sugar and milk");
    }

    @Override
    boolean customerWantsCondiments() {
        // 사용자 입력 등을 통해 Condiment 여부 결정 가능
        return true; // 기본적으로는 true
    }
}
```

---

## 🎯 4. 템플릿 메소드 패턴의 의도와 장점

- **중복 제거**: 알고리즘의 공통 부분은 상위 클래스에서 처리, 달라지는 부분만 하위 클래스에서 구현
- **제어 흐름 유지**: 알고리즘의 순서를 상위 클래스에서 고정할 수 있다
- **OCP**(**개방-폐쇄 원칙**): 알고리즘의 구조를 바꾸지 않고도 기능을 확장할 수 있다
- **훅**(**Hook**)을 활용하여 **하위 클래스에서 선택적으로 알고리즘 흐름을 제어**할 수 있다

---

## 🔁 5. 자바 API에서의 템플릿 메소드

템플릿 메소드는 자바의 많은 API에서도 활용된다. 예를 들어 `java.util.AbstractList`, `java.util.AbstractSet`, `javax.servlet.http.HttpServlet` 등에서 상속받아 일부 메서드만 구현하면 동작한다.

---

## 📌 정리

- **템플릿 메소드 패턴은 상위 클래스에서 알고리즘의 뼈대를 정의하고, 세부 구현은 하위 클래스에 위임하는 구조**이다.
- 이를 통해 **코드의 재사용성, 유지보수성, 확장성**을 높일 수 있다.
- **훅 메서드**(**hook**)를 통해 **알고리즘의 유연한 제어가 가능**하다.
