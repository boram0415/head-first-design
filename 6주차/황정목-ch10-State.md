# 헤드 퍼스트 디자인 패턴 - Chapter 10 요약 및 정리

## 상태 패턴: 객체의 내부 상태를 객체화하여 상태에 따라 행동을 바꾸자

이번 챕터에서는 **상태 패턴**(**State Pattern**)을 배웠다.

상태 패턴은 **객체의 상태를 클래스로 분리하고, 상태에 따라 객체의 행동을 변경할 수 있도록 만드는 패턴**이다.
즉, 객체의 행동이 **내부 상태에 따라 달라질 수 있으며**, 새로운 상태를 추가해도 기존 코드를 크게 변경하지 않는다.

---

## 1. 자판기 예제(Gumball Machine)

우리는 **껌볼 자판기** 애플리케이션을 만든다고 가정한다.

자판기는 다음과 같은 상태를 가진다:

- 동전 없음(NoQuarterState)
- 동전 있음(HasQuarterState)
- 알맹이 판매 중(SoldState)
- 품절(SoldOutState)

### 문제점: 상태 조건이 if-else로 난무한다

```java
public void insertQuarter() {
    if (state.equals("noQuarter")) {
        state = "hasQuarter";
    } else if (state.equals("hasQuarter")) {
        System.out.println("이미 동전이 들어있습니다.");
    } // ... 이하 생략
}
```

이렇게 구현하면 상태가 추가되거나 수정될 때마다 `if-else` 블록을 수정해야 하므로, **OCP 원칙을 위반**하게 된다.

---

## 2. 상태 패턴 구조로 리팩토링

### 상태 인터페이스

```java
public interface State {
    void insertQuarter();
    void ejectQuarter();
    void turnCrank();
    void dispense();
}
```

### GumballMachine 클래스

```java
public class GumballMachine {
    State soldOutState;
    State noQuarterState;
    State hasQuarterState;
    State soldState;

    State state;
    int count = 0;

    public GumballMachine(int count) {
        soldOutState = new SoldOutState(this);
        noQuarterState = new NoQuarterState(this);
        hasQuarterState = new HasQuarterState(this);
        soldState = new SoldState(this);

        this.count = count;
        state = (count > 0) ? noQuarterState : soldOutState;
    }

    public void insertQuarter() {
        state.insertQuarter();
    }

    public void ejectQuarter() {
        state.ejectQuarter();
    }

    public void turnCrank() {
        state.turnCrank();
        state.dispense();
    }

    public void setState(State state) {
        this.state = state;
    }

    public void releaseBall() {
        System.out.println("알맹이가 나옵니다...");
        if (count != 0) {
            count--;
        }
    }

    public int getCount() {
        return count;
    }

    public State getNoQuarterState() { return noQuarterState; }
    public State getHasQuarterState() { return hasQuarterState; }
    public State getSoldState() { return soldState; }
    public State getSoldOutState() { return soldOutState; }
}
```

---

## 3. 상태 클래스들 구현하기

### NoQuarterState (동전 없음)

```java
public class NoQuarterState implements State {
    GumballMachine machine;

    public NoQuarterState(GumballMachine machine) {
        this.machine = machine;
    }

    public void insertQuarter() {
        System.out.println("동전을 넣었습니다.");
        machine.setState(machine.getHasQuarterState());
    }

    public void ejectQuarter() {
        System.out.println("동전이 없습니다.");
    }

    public void turnCrank() {
        System.out.println("동전을 넣어야 합니다.");
    }

    public void dispense() {
        System.out.println("알맹이를 내보낼 수 없습니다.");
    }
}
```

### HasQuarterState (동전 있음)

```java
public class HasQuarterState implements State {
    GumballMachine machine;

    public HasQuarterState(GumballMachine machine) {
        this.machine = machine;
    }

    public void insertQuarter() {
        System.out.println("이미 동전이 있습니다.");
    }

    public void ejectQuarter() {
        System.out.println("동전을 반환합니다.");
        machine.setState(machine.getNoQuarterState());
    }

    public void turnCrank() {
        System.out.println("손잡이를 돌렸습니다.");
        machine.setState(machine.getSoldState());
    }

    public void dispense() {
        System.out.println("아직 알맹이를 내보낼 수 없습니다.");
    }
}
```

### SoldState (판매 중)

```java
public class SoldState implements State {
    GumballMachine machine;

    public SoldState(GumballMachine machine) {
        this.machine = machine;
    }

    public void insertQuarter() {
        System.out.println("잠시만 기다려 주세요. 알맹이를 내보내는 중입니다.");
    }

    public void ejectQuarter() {
        System.out.println("이미 손잡이를 돌렸습니다.");
    }

    public void turnCrank() {
        System.out.println("손잡이는 한 번만 돌려주세요!");
    }

    public void dispense() {
        machine.releaseBall();
        if (machine.getCount() > 0) {
            machine.setState(machine.getNoQuarterState());
        } else {
            System.out.println("재고가 없습니다!");
            machine.setState(machine.getSoldOutState());
        }
    }
}
```

### SoldOutState (품절)

```java
public class SoldOutState implements State {
    GumballMachine machine;

    public SoldOutState(GumballMachine machine) {
        this.machine = machine;
    }

    public void insertQuarter() {
        System.out.println("매진되었습니다. 동전을 넣을 수 없습니다.");
    }

    public void ejectQuarter() {
        System.out.println("동전이 없습니다.");
    }

    public void turnCrank() {
        System.out.println("매진되었습니다.");
    }

    public void dispense() {
        System.out.println("내보낼 알맹이가 없습니다.");
    }
}
```

---

## 상태 패턴의 장점

- **상태별 행동을 객체로 분리**하여 코드가 깔끔해진다.
- 새로운 상태를 쉽게 추가할 수 있으며, OCP(개방-폐쇄 원칙)를 만족한다.
- 객체의 행동을 변경할 때, 조건문 대신 **상태 객체 교체로 해결**할 수 있다.
- **상태 전이 로직을 상태 객체에 위임**할 수 있어 유지보수가 편하다.

---

## 결론

- **상태 패턴은 객체의 상태에 따라 행동이 바뀌어야 하는 경우에 적합한 패턴이다.**
- `if-else` 나 `switch` 문으로 상태를 분기하던 복잡한 코드가 **각 상태 클래스로 깔끔하게 분리**된다.
- 상태 간 전이 또한 각 클래스에 위임하므로 **유연한 코드 구성과 확장**이 가능하다.

게임 상태 관리, UI 상태 처리, 자판기/ATM 시스템 등 **상태 전이가 중요한 시스템**에서 특히 유용하게 사용된다.
