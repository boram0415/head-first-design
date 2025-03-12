# Ch4. 객체지향 빵 굽기-팩토리 패턴

## 문제 1) 새로운 인스턴스가 추가할 때마다 코드를 수정해야 한다.

구상 클래스의 인스턴스가 여러 개 있고, 그 인스턴스 형식은 실행 시에 주어진 조건에 따라 결정이 되는 코드가 있다. 이런 코드를 변경하거나 확장해야 할 때는 코드를 다시 확인하고 새로운 코드를 추가하거나 기존 코드를 제거해야 한다. 



```
Pizza orderPizza(){
    Pizza pizza;

    if(type.equals("cheese")){
        pizza = new CheesePizza();
    } else if(type.equals("greek")){
        pizza = new GreekPizza();
    } else if(type.equals("pepperoni")){
        pizza = new PepperoniPizza();
    }

    pizza.prepare();
    pizza.bake();
    pizza.cut();
    pizza.box();

    return pizza;
}
```


## 해결 1) 바뀌는 부분을 찾아서 캡슐화를 하자

피자 종류가 추가될 때 마다 if문을 추가해줘야 한다. 이 부분이 계속 바뀌므로 객체 생성 부분을 캡슐화를 한다.


```
public class PizzaStore{
    SimplePizzaFactory factory;

    public PizzaStore(SimplePizzaFactory factory){
        this.factory = factory;
    }

    public Pizza orderPizza(String type){
        Pizza pizza;

        // 변경된 부분
        pizza = factory.createPizza(type);

        pizza.prepare();
        pizza.bake();
        pizza.cut();
        pizza.box();

        return pizza;
    }
}

public class SimplePizzaFactory{
    public Pizza createPizza(String type){
        Pizza pizza = null;

        // orderPizza() 메서드에서 뽑아낸 코드
        if(type.equals("cheese")){
            pizza = new CheesePizza();
        } else if(type.equals("greek")){
            pizza = new GreekPizza();
        } else if(type.equals("pepperoni")){
            pizza = new PepperoniPizza();
        }
    }

}
```

SimplePizzaFactory 클래스는 클라이언트가 받을 피자만 만드는 역할을 한다.

<img src="https://github.com/user-attachments/assets/d07cfa50-6b27-47bc-9d69-98e8cb114b0d" style="width:400px; height:300px;"/>

### (간단한) 팩토리(Facotry)
객체 생성을 처리하는 클래스를 팩토리(Factory)라고 부른다. 

위 코드처럼 간단한 팩토리(Simple Factory)는 디자인 패턴이라기 보다는 프로그래밍에서 자주 쓰이는 관용구에 가깝다.

장점 1. 객체 생성 코드를 전부 한 객체 또는 메소드에 넣으면 중복되는 내용을 제거할 수 있고, 나중에 관리할 때도 한 군데에만 신경을 쓰면 된다.

장점 2. 객체 인스턴스를 만들 때 인터페이스만 있으면 된다.


## 문제 2) 다양한 스타일의 피자를 추가해보자.
각 지점마다 그 지역의 특성과 입맛을 반영한 다양한 스타일의 피자(뉴욕 스타일, 시카고 스타일, 캘리포니아 스타일 등)을 만들어보자.

## 해결 2) PizzaStore 클래스를 추상 클래스로 변경하고 createPizza 메소드를 추상 메소드로 변경하자.


```
public abstract class PizzaStore{

    public Pizza orderPizza(String type){
        Pizza pizza;

        pizza = factory.createPizza(type);

        pizza.prepare();
        pizza.bake();
        pizza.cut();
        pizza.box();

        return pizza;
    }

    abstract Pizza createPizza(String type);
}

```

팩토리 메소드는 객체 생성을 서브클래스에 캡슐화할 수 있다. 그러면 슈퍼클래스에 있는 클라이언트 코드와 서브클래스에 있는 객체 생성 코드를 분리할 수 있다.

SimplePizzaFactory 대신 PizzaStore의 서브클래스를 만든다. 그리고 지역별 특성에 맞게 createPizza() 메소드만 구현하면 된다.

```
public class NYPizzaStore extends PizzaStore{
    Pizza createPizza(String item){
        Pizza pizza = null;

        // orderPizza() 메서드에서 뽑아낸 코드
        if(item.equals("cheese")){
            pizza = new NYStyleCheesePizza();
        } else if(item.equals("veggie")){
            pizza = new NYStyleVeggiePizza();
        } else if(item.equals("pepperoni")){
            pizza = new NYStylePepperoniPizza();
        } else {
            return null;
        }
    }
}
```

createPizza() 메소드가 팩토리 메소드이다. 이 메소드에서 제품(객체)를 생산한다.

### Pizza 클래스 만들기

```
public abstract class Pizza{
    String name;
    String dough;
    String sauce;
    List<String> toppings = new ArrayList<String>();
    void prepare(){
       System.out.println("준비 중: " + name);
       System.out.println("도우를 돌리는 중...");
       System.out.println("소스를 뿌리는 중...");
       System.out.println("토핑을 올리는 중 :");
       for(String topping : toppings){
            System.out.println(" " + topping);
       }
    }

    void bake(){
        System.out.println("굽기...");
    }

    void cut(){
        System.out.println("피자를 사선으로 자르기...");
    }

    void box(){
        System.out.println("상자에 피자 담기");
    }

    public String getName(){
        return name;
    }
}

public class NYStyleCheesePizza extends Pizza {
    public NYStyleCheesePizza(){
        name = "뉴욕 스타일 소스와 치즈 피자";
        dough = "씬 크러스트 도우";
        sauce = "마리나라 소스";

        toppings.add("잘게 썬 레지아노 치즈");
    }
}
```


실행 코드

```
public class PazzaTestDrive{
    public static void main(String[] args){
        PizzaStore nyStore = new NYPizzaStore();

        Pizza pizza = nyStore.orderPizza("cheese");
    }
}
```


## 팩토리 메소드 패턴(Factory Method Pattern)
팩토리 메소드 패턴에서는 객체를 생성할 때 필요한 인터페이스를 만든다. 어떤 클래스의 인스턴스를 만들지는 서브클래스에서 결정한다. 팩토리 메소드 패턴을 사용하면 클래스 인스턴스 만드는 일을 서브클래스에게 맡기게 된다.


<img src="https://github.com/user-attachments/assets/fae270d2-c7be-41e8-8681-cce522d7babe" style="width:400px; height:300px;"/>

Q1. 구상 생산자 클래스가 하나밖에 없다면 팩토리 메소드 패턴을 쓸 필요가 있나요?

A1. 구상 생산자 클래스가 하나밖에 없더라도 팩토리 메소드 패턴은 충분히 유용하다. 제품을 생산하는 부분과 사용하는 부분을 분리할 수 있기 때문이다. 다른 제품을 추가하거나 제품 구성을 변경하더라도 Creator 클래스가 ConcreteProduct와 느슨하게 결합되어 있으므로 Creator는 건드릴 필요가 없다.

Q2. 간단한 팩토리와 팩토리 메소드 패턴의 차이

A2. 첫번째는 팩토리 메소트 패턴에서 피자를 리턴하는 클래스가 서브클래스이다. 두번째는 간단한 팩토리는 객체 생성을 캡슐화하는 방법을 사용하지만 생성하는 제품을 마음대로 변경할 수 없다.


<img src="https://github.com/user-attachments/assets/b3a33f46-b076-4fdb-a972-e9836caea605" style="width:400px; height:300px;"/>


## 디자인 원칙 - 추상화된 것에 의존하게 만들고 구상 클래스에 의존하지 않게 만든다.

<img src="https://github.com/user-attachments/assets/f2a30c4f-2ede-4899-bef1-176497931927" style="width:400px; height:300px;"/>

고수준 구성 요소가 저수준 구성 요소에 의존하면 안 되며, 항상 추상화에 의존하게 만들어야 한다는 뜻이다. PizzaStore는 고수준 구성 요소라고 할 수 있고, Pizza 클래스는 저수준 구성 요소라고 할 수 있다. 

PizzaStore 클래스는 구상 피자 클래스에 의존하고 있다는 사실을 확실하게 알 수 있다.

<img src="https://github.com/user-attachments/assets/572aaa81-9bce-406b-8452-fef5792ad353" style="width:400px; height:300px;"/>

팩토리 메소드 패턴을 적용하면 의존성을 뒤집을 수 있다.


### 의존성 뒤집기 원칙을 지키는 방법
1. 변수에 구상 클래스의 레퍼런스를 저장하지 말자.
   
    new 연산자를 사용하면 구상 클래스의 레퍼런스를 사용하게 된다. 그러니 팩토리를 써서 구상 클래스의 레퍼런스를 변수에 저장하는 일을 미리 방지하자.
2. 구상 클래스에서 유도된 클래스를 만들지 말자.
   
    구상 클래스에서 유도된 클래스를 만들면 특정 구상 클래스에 의존하게 된다. 인터페이스나 추상 클래스처럼 추상화된 것으로부터 클래스를 만들어야 한다.
3. 베이스 클래스에 이미 구현되어 있는 메소드를 오버라이드하지 말자.
   
   이미 구현되어 있는 메소드를 오버라이드한다면 베이스 클래스가 제대로 추상화되지 않는다. 베이스 클래스에서 메소드를 정의할 때는 모든 서브클래스에서 공유할 수 있는 것만 정의해야하 한다.


## 문제 3) 지역 별로 원재료군을 관리하기
## 해결 3) 원재료 팩토리 만들기


PizzaIngredientFactory는 서로 관련된 제품군(피자를 만들 때 필요한 모든 제품)을 만드는 방법을 정의하는 추상 인터페이스다. - 추상 팩토리
```
public interface PizzaIngredientFactory{
    public Dough createDough();
    public Source createSource();
    public Cheese createCheese();
    public Veggies[] createVeggies();
    public Pepperoni createPepperoni();
    public Clams createClam();
}
```

```
public class NYPizzaIngredientFactory implements PizzaIngredientFactory{
    public Dough createDough(){
        return new ThinCrustDough();
    }

    public Sauce createSauce(){
        return new MarinaraSauce();
    }

    public Cheese createCheese(){
        return new ReggianoCheese();
    }

    public Veggies[] createVeggies(){
        Veggies veggies[] = {new Galic(), new Onion(), new Mushroom()};
        return veggies;
    }

    public Pepperoni createPepperoni(){
        return new SlicedPepperoni();
    }

    public Clams createClam(){
        return new FreshClams();
    }
}
```

```
public abstract class Pizza{
    String name;

    Dough dough;
    Sauce sauce;
    Veggies veggies[];
    Cheese cheese;
    Pepperoni pepperoni;
    Clams clam;

    abstract void prepare();

    ..
}
```

```
public class CheesePizza extends Pizza{
    PizzaIngredientFactory ingredientFactory;

    public CheesePizza(PizzaIngredientFactory ingredientFactory){
        this.ingredientFactory = ingredientFactory;
    }

    void prepare(){
        dough = ingredientFactory.createDough();
        sauce = ingredientFactory.createSauce();
        cheese = ingredientFactory.createCheese();
    }
}
```

뉴욕 지점에는 뉴욕 피자 원재료 팩토리를 전달해 줘야한다. 

```
public class NYPizzaStore extends PizzaStore{
    protected Pizza createPizza(String item){
        Pizza pizza = null;

        PizzaIngredientFactory ingredientFactory = 
            new NYPizzaIngredientFactory();

        // orderPizza() 메서드에서 뽑아낸 코드
        if(item.equals("cheese")){
            pizza = new CheesePizza(ingredientFactory);
        } else if(item.equals("veggie")){
            pizza = new VeggiePizza(ingredientFactory);
        } else if(item.equals("pepperoni")){
            pizza = new PepperoniPizza(ingredientFactory);
        } else {
            return null;
        }
    }
}
```

### 추상 팩토리 패턴(Abstract Factory Pattern)
추상 팩토리 패턴은 구상 클래스에 의존하지 않고도 서로 연관되거나 의존적은 객체로 이루어진 제품군을 생산하는 인터페이스를 제공한다. 구상 클래스는 서브클래스에서 만든다.

<img src="https://github.com/user-attachments/assets/ba02c116-87f2-49cc-b018-094242e291e2" style="width:400px; height:300px;"/>

<img src="https://github.com/user-attachments/assets/a99bcf23-f69b-406c-b5a4-0bb85cba61c7" style="width:400px; height:300px;"/>



### 팩토리 메소드 패턴과 추상 팩토리 패턴 차이
|팩토리 메소드 패턴|추상 팩토리 패턴|
|---|---|
|상속으로 객체 생성(클래스를 확장하고 팩토리 메소드를 오버라이드)|객체 구성(composition)으로 생성|
|서브 클래스로 객체 생성|제품군을 만드는 추상 형식|
||여러 제품군을 생성할 수 있다.|
|한가지의 제품만 생산 가능|새로운 제품을 추가하려면 인터페이스를 바꿔야 한다.|

<img src="https://github.com/user-attachments/assets/ebf5c082-d4fa-45bb-82be-7fdd3263bfaa" style="width:400px; height:300px;"/>

<img src="https://github.com/user-attachments/assets/580ac1ed-1574-4185-92b8-5b3de02c6623" style="width:400px; height:300px;"/>