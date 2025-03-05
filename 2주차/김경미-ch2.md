# 객체들에게 연릭 돌리기-옵저버 패턴

## 구현 목표
1. 디스플레이 구현하기
   (현재는 3가지 디스플레이 - 현재 조건, 기상 통계, 기상 예보)
2. 새로운 값이 들어올 때마다, 즉 measurementsChanged()메소드가 호출될 때마다 WeaterData에서 디스플레이를 업데이트하기
3. 디스플레이가 추가로 더 생기더라도 확장성 있게 구현하기
   
## 기존 코드

 <img src="https://github.com/user-attachments/assets/718be32f-9e72-4f3b-97d3-b61c7ce8d1ed" style="width:400px; height:300px;"/>

### 문제점
1. 인터페이스가 아닌 구체적인 구현을 바탕으로 코딩을 하고 있다.
2. 새로운 디스플레이 항목이 추가될 때마다 코드를 변경해야 한다.
3. 실행 중에 디스플레이 항목을 추가하거나 제거할 수 없다.
4. 바뀌는 부분을 캡슐화하지 않았다.

## 해결 - 옵저버 패턴 적용하기
### 옵저버 패턴

 <img src="https://github.com/user-attachments/assets/44673687-46f5-46a3-8bea-a123ee58a41a" style="width:400px; height:300px;"/>

신문사 = 주제(Subject)
신문사는 새로운 신문이 나올 때마다 구독자에게 전달한다.

구독자 = 옵저버(Observer)
구독자는 신문을 구독 신청, 해지가 가능하다.
구독하는 동안 새로운 신문을 받아 볼 수 있다.

#### 옵저버 패턴(Observer Pattern)
옵저버 패턴은 한 객체의 상태가 바뀌면 그 객체에 의존하는 다른 객체에게 연락이 가고 자동으로 내용이 갱신되는 방식으로 일대다(one-to-many) 의존성을 정의한다.

 <img src="https://github.com/user-attachments/assets/b5fd88da-c409-40ec-830a-6549aa05f114" style="width:400px; height:300px;"/>

옵저버는 여러 개가 있을 수 있으며, 주제에서 상태가 바뀌었다는 사실을 알려 주길 기다리는, 주제에 의존적인 성질을 가진다.

옵저버 패턴은 여러 가지 방법으로 구현할 수 있지만, 보통은 주제 인터페이스와 옵저버 인터페이스가 들어있는 클래스 디자인으로 구현한다.


### 느슨한 결합(Loose Coupling)
느슨한 결합은 객체들이 상호작용할 수는 있지만, 서로를 잘 모르는 관계를 의미한다.

1) 주제는 옵저버가 특정 인터페이스(Observer 인터페이스)를 구현한다는 사실만 안다.
- 옵저버의 구상 클래스가 무엇인지, 옵저버가 무엇을 하는지는 알 필요도 없다.
2) 옵저버는 언제든지 새로 추가할 수 있다.
3) 새로운 형식의 옵저버를 추가할 때도 주제를 변경할 필요가 전혀 없다.
4) 주제와 옵저버는 서로 독립적으로 재사용할 수 있다.
- 주제나 옵저버를 다른 용도로 활용할 일이 있다고 해도 손쉽게 재사용할 수 있다. 그 둘이 서로 단단하게 결합되어 있지 않기 때문이다.
5) 주제나 옵저버가 달라져도 서로에게 영향을 미치지 않는다.


-> 디자인 원칙 : 상호작용하는 객체 사이에는 가능하면 느슨한 결합을 사용해야 한다.
느슨하게 결합하는 디자인을 사용하면 변경 사항이 생겨도 무난히 처리할 수 있는 유연한 객체지향 시스템을 구축할 수 있다. 객체 사이의 상호의존성을 최소화 할 수 있기 때문이다.


### 수정 1 - 주제(Subject)가 주는 데이터를 옵저버(Observer)가 받기
푸시(Push) 방법 : 주제가 옵저버에게 상태를 알리는 방식


<img src="https://github.com/user-attachments/assets/a1b179d3-117c-4ff5-9de8-0708cb585946" style="width:400px; height:300px;"/>

<img src="https://github.com/user-attachments/assets/47486711-d0fa-48c0-8e93-1c0d3e439a5d" style="width:400px; height:300px;"/>

<img src="https://github.com/user-attachments/assets/9df60bc7-8874-4d6a-8807-b1c26d20ad03" style="width:400px; height:300px;"/>

### 수정 2 - 옵저버(Observer)가 필요한 데이터만 받기
풀(Pull) 방법 : 옵저버가 주제로부터 상태를 끌어오는 방식(선호!)

<img src="https://github.com/user-attachments/assets/925d2089-cc10-4cbe-b74a-6db56581068e" style="width:400px; height:300px;"/>

getter메소드를 사용해 필요한 데이터를 가져오도록 수정한다.




