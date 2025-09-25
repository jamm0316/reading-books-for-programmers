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
