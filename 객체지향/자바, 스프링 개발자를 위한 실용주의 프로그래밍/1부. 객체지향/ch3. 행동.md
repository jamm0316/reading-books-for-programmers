> [!important]
> [[ch.1 절차지향과 비교하기#1.2 TDA 원칙](https://github.com/jamm0316/reading-books-for-programmers/blob/main/%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5/%EC%9E%90%EB%B0%94%2C%20%EC%8A%A4%ED%94%84%EB%A7%81%20%EA%B0%9C%EB%B0%9C%EC%9E%90%EB%A5%BC%20%EC%9C%84%ED%95%9C%20%EC%8B%A4%EC%9A%A9%EC%A3%BC%EC%9D%98%20%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D/1%EB%B6%80.%20%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5/Ch1.%20%EC%A0%88%EC%B0%A8%EC%A7%80%ED%96%A5%EA%B3%BC%20%EB%B9%84%EA%B5%90%ED%95%98%EA%B8%B0.md#12-tda-%EC%9B%90%EC%B9%99)] 은 객체를 행동하게 만든다.

만약 자동차를 만들어달라는 요구를 받으면 2가지 방법으로 개발하는 개발자가 있다.
1. 필요한 속성을 먼저 떠올리는 개발자
2. 필요한 행동을 먼저 떠올리는 개발자

두 경우 아래와 같이 다른 코드가 나온다.

- 속성을 떠올린 개발자
```java
public class Car {
    private Frame frame;
    private Engine Engine;
    ...
}
```

- 행동을 떠올린 개발자
```java
public class Car {
    public void drive();
    public void changeDirection(float amount);
    public void accelerate(float speed);
    public void decelerate(float speed);
}
```

이 둘의 차이점은 '행동을 떠올린 개발자'의 코드가 좀 더 객체지향적이란 것이다.
이유는 객체 끼리 협력하기 위해 상대방에게 행동을 요구할 수 있어야하므로 행동 위주의 사고를 하는 것이 개체지향에서 훨씬 유리하기 때문이다.

객체를 구분짓는 요인은 데이터가 아닌 행동이다.

이 것이 유리한 이유는 Car가 아닌 행동위주로 생각하면 Vehicle이란 객체가 나올 수 있기 때문이다.
나아가 이 객체는 역할을 맡을 수 있게 되고 일종의 계약 형태로 interface를 만들 수 있게 된다.

그렇다면 Car가 아닌 Bicycle이란 객체에도 유연하게 대처가 가능하다.

### 3.1 덕타이핑

> 행동이 같다면 같은 객체로 본다.

### 3.2 행동과 구현

> 행동을 정의할 때 구현은 생각하지 않는다.
> 이유는 병렬 작업과 협업에 용이하기 때문

구현을 생각하면 자연스럽게 필드들이 떠오른다.
그렇다면 행동을 생각하면서 기껏 객체지향 관점으 코드를 만들어 놨더니 다시 데이터 위주의 사고로 돌아온 꼴이 된다.

따라서 행동을 고민하는 순간에는 순수하게 이 클래스에 어떤 동작을 시킬 수 있을 것인지만 고민하는 것이 좋다.
'어떻게' 동료들과 협업을 위해 비워두자.

### 3. 3 인터페이스

> '나를 조작하고 싶으면 이런 메시지르 보내면 된다'고 외부에 알려주는 수단

- API(Application Programming Interface): 애플리케이션을 조작하고 싶을 때 보내면 되는 메시지 모음
- UI(User Interface): 사용자가 프로그램을 조작하고 싶을 때 보내는 메시지 모음

이러한 개념 덕분에 자연스럽게 이해되는 것은
interface로 선언된 객체에는 private는 선언 불가하다.
외부에 알려주는 수단이므로 public이 기본이다.

### 3.4 행동과 역할

행동위주로 생각했더니 객체의 역할이 떠오른다.
즉, ride, run, stop 등을 떠올렸더니 Vihecle이 나온다.

이는 소통할 때 핵심으로 들어가는데 좋은 역할을 한다.

- 클라이언트: 자동차 객체를 만들어주세요.

이런 질문에 아래와 같이 답변할 수 있게된다.
- 자동차는 어떤 행동을 하는 객체인가요?
- 꼭 자동차여야 하나요?
- 자동차라는 클래스를 만들어서 달성하려는 목표가 무엇인가요?

위 질문을 통해 클라이언트는 본질에 더욱 다가갈 수 있다.

> [!important]
> 구현에 집중한 코드는 확장되는 요구사항에 유연하게 대처할 수 없다.
> 역할에 집중해야 유연한 설계를 얻을 수 있다.
> 반복적이고 의식적으로 객체가 어떤 행동을 해야하는지 고민해야한다.
> 나아가 행동들을 모아 구조적으로 타당한 역할을 만들 수 있을지 고민해야 한다.


### 3.5 메서드

메서드와 함수의 차이점
- 함수: input에 대응 되는 output이 명확하다
- 메서드: input에 따라 output이 다를 수 있다.

예를 들어 아래와 같은 코드는 함수다.
input에 대응하는 명확한 output이 있기 때문이다.

```java
class User {

    public ride(String type, Object object) {
        switch (type) {
            case "CAR":
                ((Car) object).ried();
                break;
			case "BICYCLE":
			((Bicycle) object).ried();
			break;
        }
    }
}
```

그러나 아래와 같은 코드는 메서드다.
```java
class Car implement Vehicle {
    void ride() {};
    void run() {};
    void stop() {};
}

class Bicycle implement Vehicle {
    void ride() {};
    void run() {};
    void stop() {};
}
class User {
    void ride(Vehicle vehicle) {
        vehicle.ride();
    }
}
```

여기에서 user의 ride는 실행되기 전까지 output을 알 수 없다.

즉, 함수는 절차지향적이고 메서드는 객체지향적이다.
