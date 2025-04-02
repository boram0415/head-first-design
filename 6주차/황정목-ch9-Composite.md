# 헤드 퍼스트 디자인 패턴 - Chapter 9 (Part 2) 요약 및 정리

## 컴포지트 패턴: 트리 구조를 사용해 개별 객체와 객체 그룹을 동일하게 다루자!

이번에는 반복자 패턴과 함께 소개된 **컴포지트 패턴**(**Composite Pattern**)을 배운다.

컴포지트 패턴은 **객체들을 트리 구조로 구성하여, 개별 객체와 객체 그룹을 동일하게 다룰 수 있도록 하는 패턴**이다.

즉, 클라이언트가 **Leaf(잎) 객체와 Composite(그룹) 객체를 구분하지 않고 동일한 방식으로 처리**할 수 있도록 만들어준다.

---

## 1. 메뉴 시스템을 위한 객체 구조 설계

`MenuItem`은 하나의 메뉴 항목이고, `Menu`는 여러 개의 메뉴 항목 또는 하위 메뉴를 포함할 수 있다.

`Menu`와 `MenuItem` 모두 **동일한 인터페이스를 사용하여** 처리되도록 설계할 수 있다.

---

## 2. 공통 인터페이스: MenuComponent

```java
public abstract class MenuComponent {
    // Leaf와 Composite 모두가 가질 수 있는 공통 인터페이스 정의
    public void add(MenuComponent component) {
        throw new UnsupportedOperationException();
    }
    public void remove(MenuComponent component) {
        throw new UnsupportedOperationException();
    }
    public MenuComponent getChild(int i) {
        throw new UnsupportedOperationException();
    }

    public String getName() {
        throw new UnsupportedOperationException();
    }
    public String getDescription() {
        throw new UnsupportedOperationException();
    }
    public double getPrice() {
        throw new UnsupportedOperationException();
    }
    public boolean isVegetarian() {
        throw new UnsupportedOperationException();
    }

    public void print() {
        throw new UnsupportedOperationException();
    }
}
```

- `MenuComponent`는 모든 메뉴 요소의 **공통 인터페이스 역할**을 하며, 디폴트로 예외를 던진다. 필요한 서브클래스에서만 해당 기능을 오버라이드해서 사용하면 된다.

---

## 3. Leaf 클래스: MenuItem

```java
public class MenuItem extends MenuComponent {
    String name;
    String description;
    boolean vegetarian;
    double price;

    public MenuItem(String name, String description, boolean vegetarian, double price) {
        this.name = name;
        this.description = description;
        this.vegetarian = vegetarian;
        this.price = price;
    }

    public String getName() { return name; }
    public String getDescription() { return description; }
    public boolean isVegetarian() { return vegetarian; }
    public double getPrice() { return price; }

    public void print() {
        System.out.print(" " + getName());
        if (isVegetarian()) System.out.print(" (v)");
        System.out.println(", " + getPrice());
        System.out.println("    -- " + getDescription());
    }
}
```

---

## 4. Composite 클래스: Menu

```java
public class Menu extends MenuComponent {
    List<MenuComponent> menuComponents = new ArrayList<>();
    String name;
    String description;

    public Menu(String name, String description) {
        this.name = name;
        this.description = description;
    }

    public void add(MenuComponent component) {
        menuComponents.add(component);
    }

    public void remove(MenuComponent component) {
        menuComponents.remove(component);
    }

    public MenuComponent getChild(int i) {
        return menuComponents.get(i);
    }

    public String getName() { return name; }
    public String getDescription() { return description; }

    public void print() {
        System.out.println("\n" + getName() + ", " + getDescription());
        System.out.println("---------------------");
        for (MenuComponent component : menuComponents) {
            component.print();
        }
    }
}
```

- `Menu`는 `MenuComponent`를 자식으로 가질 수 있고, 하위에 또 다른 `Menu`나 `MenuItem`을 포함할 수 있다.
- **트리 구조를 통해 메뉴 → 하위 메뉴 → 메뉴 아이템까지 재귀적으로 구성 가능하다.**

---

## 5. 클라이언트 코드: Waitress

```java
public class Waitress {
    MenuComponent allMenus;

    public Waitress(MenuComponent allMenus) {
        this.allMenus = allMenus;
    }

    public void printMenu() {
        allMenus.print();
    }
}
```

- `Waitress`는 Menu인지 MenuItem인지 신경 쓰지 않고 `MenuComponent`만 알면 된다.
- 새로운 메뉴나 아이템이 추가되어도 클라이언트 코드 변경 없이 동작한다!

---

## 결론

- **컴포지트 패턴**은 객체들의 트리 구조를 구성하여 **단일 객체와 복합 객체를 동일하게 처리**할 수 있게 만든다.
- 클라이언트는 복합 객체인지, 개별 객체인지 몰라도 동일한 방식으로 호출이 가능하다.
- 반복자 패턴과 함께 사용하면 **트리 구조 안의 객체들을 효과적으로 탐색**할 수 있다.
