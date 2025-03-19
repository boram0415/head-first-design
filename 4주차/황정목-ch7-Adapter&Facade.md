# 헤드 퍼스트 디자인 패턴 - Chapter 7 요약 및 정리

## 🔌 어댑터 패턴 & 퍼사드 패턴: 인터페이스를 변환하고 단순화하자!

이번 챕터에서는 **어댑터 패턴**(**Adapter Pattern**)과 **퍼사드 패턴**(**Facade Pattern**)을 배웠다.

- **어댑터 패턴**: 기존 인터페이스를 새로운 인터페이스에 맞게 변환하여 **호환성을 높이는 패턴**이다.
- **퍼사드 패턴**: 복잡한 서브시스템을 단순화하는 **고수준 인터페이스를 제공하는 패턴**이다.

---

# 🔌 어댑터 패턴(Adapter Pattern)

## 🏭 1. 어댑터 패턴이란?

어댑터 패턴은 **서로 다른 인터페이스를 가진 객체들이 함께 동작할 수 있도록 변환해주는 패턴**이다.
즉, **클라이언트가 기대하는 인터페이스를 제공하여 기존 코드와 새로운 코드가 함께 동작할 수 있도록 한다.**

### 💡 어댑터 패턴을 이해하기 위한 예제: `Duck`과 `Turkey`

우리는 `Duck`(오리) 인터페이스를 사용하는 애플리케이션을 만들었는데, 기존에 `Turkey`(칠면조) 객체를 활용해야 하는 상황이 발생했다.
칠면조는 오리와 비슷하지만 인터페이스가 다르므로 바로 사용할 수 없다. 따라서 **어댑터 패턴을 사용하여 칠면조를 오리처럼 사용할 수 있도록 변환해야 한다.**

---

## 🦆 2. Duck(오리)와 Turkey(칠면조) 인터페이스

### 🎯 Duck 인터페이스 (기존 코드)
```java
public interface Duck {
    void quack();
    void fly();
}
```

### 🎯 Turkey 인터페이스 (새로운 코드, 기존 코드와 다름)
```java
public interface Turkey {
    void gobble();
    void fly();
}
```

### 🦃 Turkey 클래스 (칠면조 구현체)
```java
public class WildTurkey implements Turkey {
    @Override
    public void gobble() {
        System.out.println("Gobble gobble!");
    }
    
    @Override
    public void fly() {
        System.out.println("I’m flying a short distance");
    }
}
```

---

## 🔄 3. 어댑터 클래스 구현 (Turkey → Duck 변환)

칠면조 객체를 **오리처럼 사용**하려면, **TurkeyAdapter**를 만들어 Duck 인터페이스를 따르도록 변환해야 한다.

```java
public class TurkeyAdapter implements Duck {
    private Turkey turkey;
    
    public TurkeyAdapter(Turkey turkey) {
        this.turkey = turkey;
    }
    
    @Override
    public void quack() {
        turkey.gobble(); // 칠면조의 gobble()을 quack()으로 변환
    }
    
    @Override
    public void fly() {
        for (int i = 0; i < 5; i++) { // 오리는 더 멀리 날기 때문에 5번 반복
            turkey.fly();
        }
    }
}
```

✅ **이제 칠면조 객체를 오리 객체처럼 사용할 수 있다!**

```java
public class AdapterTest {
    public static void main(String[] args) {
        Turkey turkey = new WildTurkey();
        Duck turkeyAdapter = new TurkeyAdapter(turkey);
        
        System.out.println("Using TurkeyAdapter as a Duck:");
        turkeyAdapter.quack(); // Gobble gobble!
        turkeyAdapter.fly();   // I'm flying a short distance (x5)
    }
}
```

✅ **어댑터 패턴을 사용하면 기존 코드(Duck 인터페이스)를 수정하지 않고도 새로운 객체(Turkey)를 활용할 수 있다!**

---

# 🏢 퍼사드 패턴(Facade Pattern)

## 🏗 1. 퍼사드 패턴이란?

퍼사드 패턴은 **복잡한 서브시스템을 단순화하는 고수준 인터페이스를 제공하는 패턴**이다.

예를 들어, 가정용 극장 시스템(Home Theater System)은 여러 가지 구성 요소(프로젝터, DVD 플레이어, 스피커 등)로 이루어져 있다.
각 기기를 개별적으로 조작하려면 너무 복잡하므로, **퍼사드(Facade)를 만들어 하나의 단순한 인터페이스를 제공하면 편리하다.**

---

## 🎬 2. 홈시어터 시스템 예제

### 💡 개별 기기 클래스 (서브시스템)
```java
public class Amplifier {
    public void on() { System.out.println("Amplifier on"); }
    public void off() { System.out.println("Amplifier off"); }
}

public class DVDPlayer {
    public void on() { System.out.println("DVD Player on"); }
    public void play(String movie) {
        System.out.println("Playing " + movie);
    }
    public void off() { System.out.println("DVD Player off"); }
}

public class Projector {
    public void on() { System.out.println("Projector on"); }
    public void off() { System.out.println("Projector off"); }
}
```

### 🏠 퍼사드 클래스 (고수준 인터페이스 제공)
```java
public class HomeTheaterFacade {
    private Amplifier amp;
    private DVDPlayer dvd;
    private Projector projector;
    
    public HomeTheaterFacade(Amplifier amp, DVDPlayer dvd, Projector projector) {
        this.amp = amp;
        this.dvd = dvd;
        this.projector = projector;
    }
    
    public void watchMovie(String movie) {
        System.out.println("Get ready to watch a movie...");
        amp.on();
        projector.on();
        dvd.on();
        dvd.play(movie);
    }
    
    public void endMovie() {
        System.out.println("Shutting movie theater down...");
        dvd.off();
        projector.off();
        amp.off();
    }
}
```

✅ **이제 클라이언트는 복잡한 서브시스템을 직접 다룰 필요 없이, 간단한 인터페이스를 사용할 수 있다!**

```java
public class HomeTheaterTest {
    public static void main(String[] args) {
        Amplifier amp = new Amplifier();
        DVDPlayer dvd = new DVDPlayer();
        Projector projector = new Projector();
        
        HomeTheaterFacade homeTheater = new HomeTheaterFacade(amp, dvd, projector);
        
        homeTheater.watchMovie("Inception");
        homeTheater.endMovie();
    }
}
```

✅ **퍼사드 패턴을 사용하면 시스템을 더 단순하고 쉽게 사용할 수 있다.**

---

## 📌 정리

🎯 **어댑터 패턴**: 기존 인터페이스를 변경하지 않고 **다른 인터페이스와 호환되도록 변환**한다.
🎯 **퍼사드 패턴**: 복잡한 시스템을 감싸서 **더 단순한 고수준 인터페이스를 제공**한다.
