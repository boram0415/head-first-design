# Ch5. 하나뿐인 특별한 객체 만들기 - 싱글턴 패턴

## 싱글턴 패턴(Singleton Pattern)
클래스 인스턴스를 하나만 만들고, 그 인스턴스로의 전역 접근을 제공한다.

예시)
1. 레지스트리 설정이 담겨 있는 객체
2. 자원 풀 관리할 때 - 연결 풀, 스레드 풀

## 고전적인 싱글턴 패턴

```
public class Singleton {
    private static Singleton uniqueInstance;

    // 기타 인스턴스 변수

    // 생성자를 private로 선언했으므로 Singleton에서만 클래스의 인스턴스를 만들 수 있다.
    private Singleton(){}

    // getInstance() 메소드는 클래스의 인스턴스를 만들어서 리턴한다.
    public static Singleton getInstance(){
        if(uniqueInstance == null){
            uniqueInstance = new Singleton();
        }

        return uniqueInstance;
    }
}
```

## 고전적인 싱글턴 문제 1) 멜티스레딩 문제

<img src="https://github.com/user-attachments/assets/9a09288a-895c-40c8-afa1-3f89f9a84998" style="width:400px; height:300px;"/>


## 고전적인 싱글턴 해결

1) 동기화 시키기
```
public class Singleton {
    private static Singleton uniqueInstance;

    // 기타 인스턴스 변수

    private Singleton(){}

    // synchronized 키워드 추가
    public static synchronized Singleton getInstance(){
        if(uniqueInstance == null){
            uniqueInstance = new Singleton();
        }

        return uniqueInstance;
    }
}
```

사실 동기화가 꼭 필요한 시점은 메소드가 시작되는 때이다. uniqueInstance 변수에 Singleton 인스턴스를 대입하면 굳이 이 메소드를 동기화된 상태로 유지할 필요가 없다.

메서드를 동기화하면 성능이 100배 정도 저하된다는 단점이 있다. 

2) Singleton 인스턴스를 처음부터 만들자
   
```
public class Singleton {
    private static Singleton uniqueInstance = new Singleton();

    // 기타 인스턴스 변수

    private Singleton(){}
   
    public static Singleton getInstance(){
        return uniqueInstance;
    }
}
```

3) DCL(Double-Checked Locking)을 써서 getInstance()에서 동기화되는 부분을 줄인다.

* volatile 키워드를 사용하면 멀티스레딩을 쓰더라도 uniqueInstance 변수가 Singleton 인스턴스로 초기화되는 과정이 올바르게 진행된다.

```
public class Singleton {
    private volatile static Singleton uniqueInstance;

    // 기타 인스턴스 변수

    private Singleton(){}
   
    public static Singleton getInstance(){
        if(uniqueInstance == null){
            // 처음에만 동기화된다.
            synchronized(Singleton.class){
                if(uniqueInstance == null){
                    uniqueInstance = new Singleton();
                }    
            }
        }

        return uniqueInstance;
    }
}
```


## 싱글턴의 문제
1. 클래스 로더 2개 이상일 경우
2. 리플렉션, 직렬화, 역직렬화
3. 느슨한 결합 원칙 위배
4. 한 클래스에서 2가지 일을 하는건 객체지향 관점에서 좋지 못한 디자인
   - '한 클래스가 한 가지만 책임지기' 원칙
   - 싱글턴은 자신의 인스턴스를 관리하는 일과 원래 그 인스턴스를 사용하고자 하는 목적에 부합하는 작업을 책임져야 한다.

-> enum 클래스를 활용해 해결 할 수 있다. enum 클래스는 자바 내부에서 1번만 생성 되도록 하기 때문이다.
enum 클래스는 인스턴스를 생성하고 보관하는 역할만을 하며, 다른 책임은 다른 클래스에서 처리할 수 있기 때문에 한 클래스에 여러 가지 책임이 부여되는 문제를 해결할 수 있다.

## 전역 변수가 싱글턴보다 나쁜 이유
||전역 변수|싱글턴|
|------|---|---|
|처음부터 끝까지 하나의 인스턴스 가지기|X|O|
|전역적인 접근|O|O|