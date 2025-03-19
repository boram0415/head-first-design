# 헤드 퍼스트 디자인 패턴 - Chapter 6 요약 및 정리

## 🎮 커맨드 패턴: 요청을 객체로 캡슐화하자!

이번 챕터에서는 **커맨드 패턴**(**Command Pattern**)을 배웠다.

**요청을 직접 처리하는 것이 아니라, 요청을 캡슐화하여 나중에 실행하거나 취소할 수 있도록 설계**하는 패턴이다.
이 패턴을 사용하면 **요청을 객체로 변환하여 재사용성과 확장성을 높일 수 있다.**

예제로 **리모컨(Remote Control) 애플리케이션**을 살펴보자.

---

## 🏠 1. 리모컨 시스템 (Remote Control)

우리는 다양한 가전제품(조명, TV, 스테레오 등)을 제어하는 리모컨 애플리케이션을 만든다고 가정하자.

### 💡 기존 방식의 문제점

리모컨이 각 기기를 직접 제어하는 코드를 포함한다면 아래와 같은 문제가 발생한다.

```java
public class RemoteControl {
    public void pressButton(String device) {
        if (device.equals("Light")) {
            System.out.println("Turning on the light");
        } else if (device.equals("TV")) {
            System.out.println("Turning on the TV");
        }
    }
}
```

✅ 문제점:
1. 새로운 기기를 추가하면 `RemoteControl` 클래스를 수정해야 한다.
2. `RemoteControl`이 직접 장치에 대한 구체적인 동작을 알고 있어 **유연성이 낮다.**
3. **요청을 취소(Undo)하거나, 나중에 실행할 방법이 없다.**

이 문제를 해결하기 위해 **커맨드 패턴**을 적용해 보자.

---

## 🏗 2. 커맨드 패턴 적용하기

커맨드 패턴을 적용하면 **요청을 객체(Command)로 캡슐화하여 실행, 취소, 저장, 조합할 수 있다.**

### 🎯 커맨드 패턴의 핵심 구조

1. **Command 인터페이스**: 실행될 동작을 추상화한다.
2. **ConcreteCommand 클래스**: 특정 동작을 실행하는 클래스로, `execute()` 메서드를 구현한다.
3. **Invoker (리모컨)**: 명령을 실행할 객체를 보관하고 실행하는 역할을 한다.
4. **Receiver (기기)**: 실제 동작을 수행하는 객체이다.

### 💡 Command 인터페이스 정의

```java
public interface Command {
    void execute();
    void undo();
}
```

### 💡 조명(Light) 기기 (Receiver)

```java
public class Light {
    public void on() {
        System.out.println("Light is ON");
    }
    
    public void off() {
        System.out.println("Light is OFF");
    }
}
```

### 💡 구체적인 커맨드 클래스 (ConcreteCommand)

```java
public class LightOnCommand implements Command {
    private Light light;
    
    public LightOnCommand(Light light) {
        this.light = light;
    }
    
    @Override
    public void execute() {
        light.on();
    }
    
    @Override
    public void undo() {
        light.off();
    }
}
```

### 💡 리모컨 (Invoker)

```java
public class RemoteControl {
    private Command command;
    
    public void setCommand(Command command) {
        this.command = command;
    }
    
    public void pressButton() {
        command.execute();
    }
    
    public void pressUndo() {
        command.undo();
    }
}
```

### 💡 클라이언트 코드 (사용 예시)

```java
public class RemoteControlTest {
    public static void main(String[] args) {
        RemoteControl remote = new RemoteControl();
        Light light = new Light();
        Command lightOn = new LightOnCommand(light);
        
        remote.setCommand(lightOn);
        remote.pressButton(); // Light is ON
        remote.pressUndo();   // Light is OFF
    }
}
```

✅ **이제 리모컨은 기기에 대한 세부 정보를 몰라도 된다.**
✅ **새로운 기기가 추가되더라도 `RemoteControl`은 변경할 필요가 없다.**
✅ **Undo 기능을 쉽게 구현할 수 있다.**

---

## 🔄 3. 여러 개의 커맨드 실행하기 (매크로 커맨드)

커맨드 패턴을 확장하여 **여러 개의 커맨드를 한 번에 실행하는 매크로(Macro) 기능을 추가할 수도 있다.**

### 💡 매크로 커맨드 클래스

```java
public class MacroCommand implements Command {
    private Command[] commands;
    
    public MacroCommand(Command[] commands) {
        this.commands = commands;
    }
    
    @Override
    public void execute() {
        for (Command command : commands) {
            command.execute();
        }
    }
    
    @Override
    public void undo() {
        for (int i = commands.length - 1; i >= 0; i--) {
            commands[i].undo();
        }
    }
}
```

### 💡 사용 예시

```java
Light light = new Light();
Stereo stereo = new Stereo();
Command lightOn = new LightOnCommand(light);
Command stereoOn = new StereoOnCommand(stereo);

Command[] partyCommands = { lightOn, stereoOn };
MacroCommand partyMode = new MacroCommand(partyCommands);

remote.setCommand(partyMode);
remote.pressButton(); // 모든 기기 ON
remote.pressUndo();   // 모든 기기 OFF
```

✅ **여러 개의 커맨드를 조합하여 한 번에 실행할 수 있다.**
✅ **실행한 명령을 반대로 실행하여 원상 복구할 수 있다.**

---

## 📌 4. 커맨드 패턴의 장점

- **요청을 객체로 캡슐화하여 유연성을 높일 수 있다.**
- **실행, 취소(Undo), 재실행(Redo) 기능을 쉽게 구현할 수 있다.**
- **명령을 큐에 저장하여 나중에 실행할 수도 있다.**
- **기존 코드를 변경하지 않고도 새로운 명령을 추가할 수 있다. (OCP 원칙 준수)**
- **매크로 기능을 활용하여 여러 개의 명령을 한 번에 실행할 수 있다.**

---

## 🏁 결론

**커맨드 패턴은 요청을 캡슐화하여 실행과 취소를 유연하게 처리할 수 있도록 도와준다.**
리모컨 같은 **GUI 기반 시스템**이나 **트랜잭션 기반 시스템**에서 자주 활용되는 패턴이다.