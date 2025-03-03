# 헤드 퍼스트 디자인 패턴 - Chapter 2 요약 및 정리

## 옵저버 패턴의 세계로 떠나기

소프트웨어에서 객체 간의 관계를 효율적으로 관리하는 방법은 중요한 문제.
이번 챕터에서는 **옵저버 패턴**을 학습하며, **날씨 모니터링 시스템**을 예제로 사용하여 이를 학습한다.

## ☀️ 1. 날씨 모니터링 시스템 (Weather Station)

날씨 정보를 여러 디스플레이 장치에 전송하는 애플리케이션을 만든다고 가정.
이 시스템에서는 **온도, 습도, 기압 정보**를 지속적으로 갱신하고 이를 여러 디스플레이에 전달한다.

### 💡 기본 설계 (WeatherData 클래스)

```java
public class WeatherData {
    private float temperature;
    private float humidity;
    private float pressure;

    public void measurementsChanged() {
        float temp = getTemperature();
        float hum = getHumidity();
        float pres = getPressure();
        
        currentConditionsDisplay.update(temp, hum, pres);
        statisticsDisplay.update(temp, hum, pres);
        forecastDisplay.update(temp, hum, pres);
    }
}
```

위 코드에서 `measurementsChanged()` 메서드가 날씨 데이터를 갱신할 때마다 모든 디스플레이 객체에 정보를 전달.

## 🚀 2. 문제 발생: 강한 결합 (Tight Coupling)

위의 설계는 아래와 같은 문제점이 있다.

1. **디스플레이 객체들이 WeatherData 클래스에 강하게 결합됨** → 새로운 디스플레이를 추가하려면 WeatherData 코드를 수정해야 함.
2. **확장성이 떨어짐** → 데이터 갱신이 필요할 때마다 여러 객체의 코드를 변경해야 함.
3. **유연하지 않음** → 특정 디스플레이를 제거하면 코드가 깨질 가능성이 있음.

💥 잘못된 확장 예시

```java
WeatherData weatherData = new WeatherData();
CurrentConditionsDisplay currentDisplay = new CurrentConditionsDisplay(weatherData);
StatisticsDisplay statisticsDisplay = new StatisticsDisplay(weatherData);
ForecastDisplay forecastDisplay = new ForecastDisplay(weatherData);
```

이러한 문제를 해결하기 위해 옵저버 패턴을 적용해보자.

---

## 🔥 3. 옵저버 패턴 적용하기

**옵저버 패턴**은 **한 객체의 상태 변화가 여러 객체에 자동으로 반영되도록 하는 패턴**.

이를 위해 두 가지 핵심 개념을 보자:
- **주제(Subject)**: 데이터 변화를 관리하고, 옵저버들에게 알림을 보내는 역할
- **옵저버(Observer)**: 주제의 상태 변경을 감지하고, 이에 따라 동작하는 객체

### 옵저버 패턴을 적용한 WeatherData

```java
import java.util.*;

public class WeatherData implements Subject {
    private List<Observer> observers;
    private float temperature;
    private float humidity;
    private float pressure;

    public WeatherData() {
        observers = new ArrayList<>();
    }
    
    public void registerObserver(Observer o) {
        observers.add(o);
    }
    
    public void removeObserver(Observer o) {
        observers.remove(o);
    }
    
    public void notifyObservers() {
        for (Observer observer : observers) {
            observer.update(temperature, humidity, pressure);
        }
    }
    
    public void measurementsChanged() {
        notifyObservers();
    }
}
```

이제 **WeatherData**가 변화를 감지하면 자동으로 옵저버들에게 알려준다.

### 옵저버 인터페이스 구현하기

```java
public interface Observer {
    void update(float temp, float humidity, float pressure);
}
```

그리고 각각의 디스플레이 클래스를 옵저버로 등록할 수 있다.

```java
public class CurrentConditionsDisplay implements Observer {
    private float temperature;
    private float humidity;
    private Subject weatherData;
    
    public CurrentConditionsDisplay(Subject weatherData) {
        this.weatherData = weatherData;
        weatherData.registerObserver(this);
    }
    
    public void update(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        display();
    }
    
    public void display() {
        System.out.println("현재 상태: " + temperature + "C, 습도: " + humidity + "%");
    }
}
```

이제 **새로운 디스플레이를 추가할 때 WeatherData를 수정할 필요가 없어졌다!**

## 🎯 옵저버 패턴의 장점

✅ **확장성이 높음** → 새로운 옵저버를 추가할 때 기존 코드를 수정할 필요 없음.  
✅ **느슨한 결합(Loose Coupling)** → 주제(Subject)와 옵저버(Observer)가 서로 직접적인 의존성이 없음.  
✅ **유지보수가 쉬움** → 특정 옵저버를 제거하거나 변경할 때도 코드 영향이 최소화됨.  

## 🔥 정리

- 옵저버 패턴은 **객체 간의 1:N 관계를 효율적으로 관리하는 방법**이다.
- **주제(Subject)와 옵저버(Observer) 사이의 느슨한 결합을 유지**하여 유연한 확장성을 제공한다.
- **예제: 날씨 모니터링 시스템**을 옵저버 패턴을 적용하여 개선할 수 있다.

옵저버 패턴을 이해하면 **이벤트 리스너, 메시지 브로커, 실시간 데이터 처리 시스템** 등 다양한 곳에서 활용할 수 있다.