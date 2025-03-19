# ch7.적응시키기-어댑터 패턴과 퍼사드 패턴

## 어댑터 패턴(Adapter Pattern)
어댑터 패턴은 특정 클래스 인터페이스를 클라이언트에서 요구하는 다른 인터페이스로 변환한다. 인터페이스가 호환되지 않아 같이 쓸 수 없었던 클래스를 사용할 수 있게 도와준다.

### Duck을 Turkey로 변환해주는 어댑터
```
public class DubckAdapter implements Turkey {
    Duck duck;
    Random random;

    DubckAdapter(Duck duck){
        this.duck = duck;
        random = new Random();
    }

    public void gobble(
        duck.quack();
    )

    public void fly(
        if(random.nextInt(5) == 0){
            duck.fly();
        }
    )
}
```

## 객체 어댑터 패턴의 객체지향 원칙

<img src="https://github.com/user-attachments/assets/482ff1a2-32b9-47d0-9625-43010242a61b" style="width:100%; height:300px;"/>

1. 어댑티를 새로 바뀐 인터페이스로 감쌀 때는 객체 구성(composition)을 사용한다. 
   어댑티의 모든 서브클래스에 어댑터를 쓸 수 있는 장점이 있다.
2. 클라이언트를 특정 구현이 아닌 인터페이스에 연결한다.
   서로 다른 백엔드 클래스로 변환시키는 여러 어댑터를 사용할 수도 있다. 이렇게 인터페이스를 기준으로 코딩했기에 타깃 인터페이스만 제대로 유지한다면 나중에 다른 구현을 추가하는 것도 가능하다.

## 객체 어댑터와 클래스 어댑터

<img src="https://github.com/user-attachments/assets/d514e795-f484-4337-8f7d-6599763d464c" style="width:100%; height:500px;"/>

앞쪽은 객체 어댑터이다. 클래스 어댑터 패턴을 사용하려면 다중 상속이 필요한데 자바에서는 다중 상속이 불가능하다.

클래스 어댑터는 타깃과 어댑터 모두 서브클래스로 만들어 사용하고, 객체 어댑터는 구성으로 어댑티에 요청을 전달한다는 점을 빼면 별 다를게 없다.


## 데코레이터 패턴과 어댑터 패턴 비교

| 비교 항목 | 데코레이터 패턴 (Decorator Pattern) | 어댑터 패턴 (Adapter Pattern) |
|-----------|---------------------------------|-------------------------------|
| **목적** | 객체의 기능을 동적으로 확장 | 기존 인터페이스를 변환하여 호환성을 제공 |
| **구조** | 기존 객체를 감싸는 방식 (Wrapper) | 기존 객체의 인터페이스를 변환 |
| **사용 시점** | 런타임에서 새로운 기능을 추가할 때 | 클라이언트가 기존 클래스와 다른 인터페이스를 요구할 때 |
| **클래스 관계** | 동일한 인터페이스를 구현하는 여러 데코레이터 클래스가 존재 가능 | 기존 인터페이스를 변환하는 중개 클래스가 존재 |
| **상속 여부** | 기존 객체와 동일한 인터페이스를 상속 또는 구현 | 서로 다른 인터페이스를 변환하여 연결 |
| **확장성** | 새로운 기능을 추가하는 데 용이 | 기존 코드 변경 없이 새 인터페이스와의 호환성을 제공 |


## 퍼사드 패턴(Facade Pattern)
퍼사드 패턴은 서브시스템에 있는 일련의 인터페이스를 통합 인터페이스로 묶어준다. 또한 고수준 인터페이스도 정의하므로 서브시스템을 더 편리하게 사용할 수 있다.

<img src="https://github.com/user-attachments/assets/652cda8b-7769-4416-b9c8-3b245a7767be" style="width:100%; height:300px;"/>

퍼사드 패턴은 단순화된 인터페이스로 서브시스템을 더 편리하게 사용하려고 쓰인다.


## 디자인 원칙 - 진짜 절친에게만 이야기해야 한다.
시스템을 디자인할 때 어떤 객체든 그 객체와 상호작용을 하는 클래스의 개수와 상호방식에 주의를 기울여야 한다. 이 원칙을 잘 따르면 여러 클래스가 복잡하게 얽혀 있어서, 시스템의 한 부분을 변경했을 때 다른 부분까지 줄줄이 고쳐야 하는 상황을 미리 방지할 수 있다.


```
// 원칙을 따르지 않은 경우
public float getTemp(){
    Thermometer thermometer = station.getThermometer();
    return thermometer.getTemperature();
}

// 원칙을 따른 경우
public float getTemp(){
    return station.getTemperature();
}
```

<img src="https://github.com/user-attachments/assets/bf73c6c1-5f78-4799-be6b-1844ed8a5b8c" style="width:100%; height:300px;"/>