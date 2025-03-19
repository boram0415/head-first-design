# ch6.호출 캡슐화하기-커맨드 패턴

## 커맨드 패턴(Command Pattern)
커맨드 패턴을 사용하면 요청 내역을 객체로 캡슐화해서 객체를 서로 다른 요청 내역에 따라 매개변수화할 수 있다. 이러면 요청을 큐에 저장하거나 로그로 기록하거나 작업 취소 기능을 사용할 수 있다.

```
public interface Command{
    public void execute(); // 실행
    public void undo(); //취소
    public void store(); // 로그 기록용 - 저장
    public void load();  // 로그 기록용 - 저장된 내용 실행
}

public class LightOnCommand implements Command {
    Light light;

    public LightOnCommand(Light light){
        this.light = light;
    } 

    public void execute(){
        light.on();
    }
}

public class LightOffCommand implements Command {
    Light light;

    public LightOffCommand(Light light){
        this.light = light;
    } 

    public void execute(){
        light.off();
    }
}
```

행동과 리시버를 한 객체에 넣고, execute()라는 메소드 하나만 외부에 공개하는 방법

-> 밖에서 볼 때는 어떤 객체가 리시버 역할을 하는지, 그 리시버가 어떤 일을 하는지 알 수 없다. 그냥 execute() 메소드를 호출하면 해당 요청이 처리된다는 사실만 알 수 있다.

## 역할 찾기
어떤 부분이 클라이언트인지, 커맨드인지, 인보커인지, 리시버인지 구분을 하자.

<img src="https://github.com/user-attachments/assets/dead9f0a-3bfa-4504-a28a-e8b5eaacfac1" style="width:400px; height:300px;"/>


### NoCommand 객체
NoCommand 객체는 일종의 널 객체(Null object)이다. 널 객체는 딱히 리턴할 객체도 없고 클라이언트가 null을 처리하지 않게 하고 싶을 때 활용하면 좋다.

```
public class NoCommand implements Command{
    public void execute(){}
}
```

예를 들어, 4~6번 슬롯처럼 리모컨에 명령이 아직 할당되지 않는 부분에 NoCommand 객체를 넣어서 execute()메소드가 호출되어도 문제가 생기지 않도록 했다.