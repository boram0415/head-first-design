# 헤드 퍼스트 디자인 패턴 - Chapter 9 요약 및 정리

## 🔁 반복자 패턴: 컬렉션의 내부 구조를 노출하지 않고 순회하자

이번 챕터에서는 **반복자 패턴**(**Iterator Pattern**)을 배웠다.

반복자 패턴은 **컬렉션의 요소들을 순차적으로 접근할 수 있도록 해주는 디자인 패턴**이다.
이 패턴을 사용하면 **컬렉션의 내부 구조를 노출하지 않고도 요소들을 반복 처리할 수 있다.**

---

## 🧺 1. 반복자 패턴이 필요한 이유

가정해보자. `PancakeHouseMenu`는 `ArrayList`로 메뉴를 저장하고, `DinerMenu`는 배열로 저장한다고 하자.
이 둘을 모두 순회하려면 `for`문이나 인덱스를 직접 사용하는 등 각기 다른 순회 로직이 필요하다.

✅ **문제점:**
- 클라이언트는 각 메뉴 클래스의 자료구조에 의존한다.
- 메뉴가 배열에서 리스트로 바뀌면 클라이언트도 수정해야 한다.

이 문제를 해결하려면, **공통된 순회 방법**을 제공해야 한다.
바로 여기에 **반복자 패턴**이 등장한다!

---

## 🔄 2. 반복자 패턴 구조

### 🎯 핵심 인터페이스: `Iterator`
```java
public interface Iterator {
    boolean hasNext();
    Object next();
}
```

### 🧺 PancakeHouseMenu와 DinerMenu의 반복자 구현

#### 🥞 PancakeHouseMenu (ArrayList 기반)
```java
public class PancakeHouseMenuIterator implements Iterator {
    List<MenuItem> items;
    int position = 0;

    public PancakeHouseMenuIterator(List<MenuItem> items) {
        this.items = items;
    }

    public boolean hasNext() {
        return position < items.size();
    }

    public Object next() {
        return items.get(position++);
    }
}
```

#### 🍽 DinerMenu (배열 기반)
```java
public class DinerMenuIterator implements Iterator {
    MenuItem[] items;
    int position = 0;

    public DinerMenuIterator(MenuItem[] items) {
        this.items = items;
    }

    public boolean hasNext() {
        return position < items.length && items[position] != null;
    }

    public Object next() {
        return items[position++];
    }
}
```

---

## 🍴 3. 메뉴 클래스에 반복자 제공하기

이제 각 메뉴 클래스에서 반복자를 제공하면 클라이언트는 자료구조에 상관없이 메뉴를 순회할 수 있다.

```java
public interface Menu {
    Iterator createIterator();
}

public class PancakeHouseMenu implements Menu {
    List<MenuItem> menuItems = new ArrayList<>();

    public PancakeHouseMenu() {
        // 메뉴 추가
    }

    public Iterator createIterator() {
        return new PancakeHouseMenuIterator(menuItems);
    }
}

public class DinerMenu implements Menu {
    static final int MAX_ITEMS = 6;
    MenuItem[] menuItems = new MenuItem[MAX_ITEMS];
    int numberOfItems = 0;

    public DinerMenu() {
        // 메뉴 추가
    }

    public Iterator createIterator() {
        return new DinerMenuIterator(menuItems);
    }
}
```

---

## 🧑‍🍳 4. 클라이언트 코드: Waitress 클래스

```java
public class Waitress {
    Menu pancakeHouseMenu;
    Menu dinerMenu;

    public Waitress(Menu pancakeHouseMenu, Menu dinerMenu) {
        this.pancakeHouseMenu = pancakeHouseMenu;
        this.dinerMenu = dinerMenu;
    }

    public void printMenu() {
        Iterator pancakeIterator = pancakeHouseMenu.createIterator();
        Iterator dinerIterator = dinerMenu.createIterator();

        System.out.println("\nMENU\n----\nBREAKFAST");
        printMenu(pancakeIterator);

        System.out.println("\nLUNCH");
        printMenu(dinerIterator);
    }

    private void printMenu(Iterator iterator) {
        while (iterator.hasNext()) {
            MenuItem item = (MenuItem) iterator.next();
            System.out.println(item.getName() + ", " + item.getPrice() + " -- " + item.getDescription());
        }
    }
}
```

- `Waitress`는 이제 메뉴가 배열인지, 리스트인지 신경 쓰지 않아도 된다!
- 메뉴가 다른 자료구조로 바뀌더라도, 클라이언트 코드는 바뀔 필요가 없다!

---

## 📦 5. 자바에서의 반복자

자바 컬렉션 프레임워크는 이미 `java.util.Iterator<E>`를 제공한다.
```java
public interface Iterator<E> {
    boolean hasNext();
    E next();
    void remove(); // 선택적
}
```

💡 `ArrayList`, `HashSet`, `HashMap` 등은 모두 반복자를 통해 순회 가능하다.
```java
List<String> list = new ArrayList<>();
Iterator<String> it = list.iterator();
while (it.hasNext()) {
    System.out.println(it.next());
}
```

---

## 📌 정리

- 반복자 패턴은 **컬렉션의 내부 구조를 노출하지 않고도 요소를 순회할 수 있게 해주는 패턴**이다.
- 서로 다른 자료구조(배열, 리스트 등)도 **동일한 방식으로 순회**할 수 있게 만든다.
- 클라이언트는 반복자의 존재만 알면 되고, 컬렉션 내부 구현에 의존하지 않는다.
- **OCP**(**개방-폐쇄 원칙**)을 만족하여 컬렉션 구현이 바뀌어도 클라이언트 코드는 유지된다.
