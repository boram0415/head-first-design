# 객체 꾸미기 - 데코레이터 패턴


커피 종류, 첨가물(휘핑, 우유, 두유, 시럽 등)에 따라 모든 클래스를 구현하면 아래와 같이 복잡해진다.

<img src="https://github.com/user-attachments/assets/58fe73ad-bb76-4d3b-a6a3-2a3f8c11a53f" style="width:400px; height:300px;"/>

## 구현 목표
1. 기존 카페 고객은 커피를 주문할 때 우유나 두유, 모카(초코릿) 등을 추가할 수 있다
2. 기존 코드를 건드리지 않고 확상으로 새로운 행동을 추가하기


### 문제1 - 슈퍼클래스 상속을 사용한 경우

<img src="https://github.com/user-attachments/assets/a9b00f79-837a-4281-9826-12427b666099" style="width:400px; height:300px;"/>

1. 첨가물 가격이 바뀔 때마다 기존 코드를 수정해야하 한다.
2. 첨가물의 종류가 많아지면 새로운 메소드를 추가해야 하고, 슈퍼클래스의 cost() 메소드도 고쳐야 한다.
3. 첨가물이 필요없는 음료인 경우 사용하지 않는 메소드를 여전히 상속받게 된다.
(예시-아이스티에 hasWhip() 같은 메소드가 상속받음)


### 해결1 - 구성과 위임으로 객체의 행동을 확장
서브클래스를 만드는 방식으로 행동을 상속받으면 그 행동은 컴파일할 때 완전히 결정된다. 게다가 모든 서브클래스에서 똑같은 행동을 상속받아야 한다. 하지만 구성으로 객체의 행동을 확장하면 실행 중에 동적으로 행동을 설정할 수 있다.


### 해결2 - 데코레이터 패턴 적용

#### 디자인 원칙 - OCP(Open-Closed Principle)
클래스는 확장에는 열려 있어야 하지만 변경에는 닫혀 있어야 한다.

특정 음료에서 시작해서 첨가물로 그 음료를 장식(decorate)해보자.

예시 - 모카와 휘핑크림을 추가한 다크 로스트 커피

<img src="https://github.com/user-attachments/assets/114e670f-3ae7-4b64-9bbb-b1a9ed125a09" style="width:400px; height:300px;"/>

<img src="https://github.com/user-attachments/assets/f7e0f07e-aec7-462d-b42d-62e41273d6f1" style="width:400px; height:300px;"/>

#### 데코레이터 패턴(Decorator Pattern)
데코레이터 패턴으로 객체에 추가 요소를 동적으로 더할 수 있다. 데코레이터를 사용하면 서브클래스를 만들 때보다 훨씬 유연하게 기능을 확장할 수 있다.


<img src="https://github.com/user-attachments/assets/e1f523fc-e20c-4da6-8622-4554b3a97c89" style="width:400px; height:300px;"/>


1. 데코레이터의 슈퍼클래스는 자신이 장식하고 있는 객체의 슈퍼클래스와 같다.
2. 한 객체를 여러 개의 데코레이터로 감쌀 수 있다.
3. 데코레이터는 자신이 감싸고 있는 객체와 같은 슈퍼클래스를 가지고 있기에 원래 객체(싸여 있는 객체)가 들어갈 자리에 데코레이터 객체를 넣어도 상관없다.
4. 데코레이터는 자신이 장식하고 있는 객체에게 어떤 행동을 위임하는 일 말고도 추가 작업을 수행할 수 있다.
5. 객체는 언제든지 감쌀 수 있으므로 실행 중에 필요한 데코레이터를 마음대로 적용할 수 있다.


### 해결 코드

<img src="https://github.com/user-attachments/assets/fe166386-6c4f-42ef-81bb-5d40256c3443" style="width:400px; height:300px;"/>

<img src="https://github.com/user-attachments/assets/83a06cd6-2031-4d3f-9541-96e2ef1e8517" style="width:400px; height:300px;"/>

<img src="https://github.com/user-attachments/assets/13805c94-b38a-4a97-bc54-d7ae1ff7ff60" style="width:400px; height:300px;"/>

<img src="https://github.com/user-attachments/assets/da75159c-4cb6-4a65-98d5-a016ad485f7b" style="width:400px; height:300px;"/>

참고 1) 슈퍼클래스가 추상 클래스라면, 그 추상 클래스에 정의된 추상 메서드들은 서브클래스에서 반드시 구현해야 한다. 추상 메서드는 구현이 없는 메서드로, 서브클래스에서 구현을 강제힌다.

참고 2) 데코레이터는 감싸고 있는 객체에 행동을 추가하는 용도로 만들어진다.


