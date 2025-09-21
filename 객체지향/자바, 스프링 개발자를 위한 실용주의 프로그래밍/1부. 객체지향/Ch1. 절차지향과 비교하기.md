## 순차지향 vs 절차지향 vs 객체지향
### 개발 패러다임, 개발 방법론

- 순차 지향(Sequential oriented): 위에서 아래로 순차적으로 실행하는 개발을 지향하는 방법론
	- ex) 어셈블리어
```
add:
	subq $8, $rsp
	leaq 16(%rsp), $rax
	...
```

- 절차 지향(Procedure oriented): procedure 단위로 실행하는 개발을 지향하는 방법론
	- ex) C언어
```c++
int add(int a, int b) {
    return a + b;
}

int main() {
	int a = 2;
	int b = 3;
	return add;
}
```

- 객체 지향(Object-oriented): 객체 단위로 실행하는 개발을 지향하는 방법론
	- ex) java

#### ⚠️ 주의
초급 자바 개발자들은 레이어드 아키텍처를 도입하며 2가지 실수를 범한다
1. 전능한 서비스 작성 
	- https://github.com/jamm0316/DaengDong/blob/main/src/main/java/com/shinhan/daengdong/member/model/service/MemberServiceImpl.java)

2. 클래스는 데이터 저장용도로만 사용
	- https://github.com/jamm0316/DaengDong/blob/main/src/main/java/com/shinhan/daengdong/member/dto/MemberDTO.java

> 이는 절차 지향적 코드이며 객체지향 패러다임이 아니다.

> 객체지향의 핵심
> - 객체에 어떤 메시지를 전달할 수 있다.
> - 객체가 어떤 책임을 진다.
> - 객체는 책임을 처리하는 방법을 스스로 알고 있다.

데이터와 비즈니스가 한 곳에 들어있는 것.
즉, 데이터 측면에서 봤을 때 어떤 행위를 하기 위해 만들어진 행동과 데이터가 한 곳에 응집되어 있는 코드가 객체지향코드에 더 가깝다.
- 좋은 예
	- 절차만 명시한 서비스
		- https://github.com/jamm0316/dailyGrowth/blob/dev/backend/src/main/java/com/todoservice/greencatsoftware/domain/member/application/MemberService.java
	- 데이터와 역할이 명시된 클래스
		- https://github.com/jamm0316/dailyGrowth/blob/dev/backend/src/main/java/com/todoservice/greencatsoftware/domain/member/domain/entity/Member.java

이 처럼 클래스에서 데이터와 그 비즈니스 로직이 명시되어 있으면 서비스는 이를 믿고 사용만 하면 된다.
이런 경우를 '캡슐화'라고 한다.

#### 💊 캡슐화를 통해 객체지향을 구현하면 좋은점

> 각자의 책임을 명확히 하여 작업을 병렬로 처리할 수 있다!

## 1.1. 책임과 역할
#DIP #다형성 #interface

> 객체지향에서는 '책임을 함수가 아닌 객체에 할당'하는 것이 중요하다.

코드 1.6에서 RestaurantChain, Stores, Order, Food 객체는 각각 calculateRevenue, calculateProfit이라는 책임을 가지고 있다.
그러나 이를 구현하기 위해 각각 클래스에 의존하는 모습을 보인다.
예를 들어 아래와 같은 경우다.

```java
class RestaurantChain {
    List<Store> stores;
    
    public long calculateRevenue(){
	    long revenue = 0;
	    for (Store store : stores) {
	        revenue += store.calculateRevenue();
	    };
    };
    public long calculateProfit(){...};
}

class Store {
    List<Order> orders;
    
    public long calculateRevenue(){};
    public long calculateProfit(){};
}
```

위 코드에서 반복적으로 등장하는 객체들의 책임이있다
`public calculateRevenue(){};`, `public calculateProfit(){};`
이렇게 공통적으로 처리하는 책임을 추상화 할 수 있다.

```java
interface Calculable {
    long calculateRevenue();
    long calculateProfit();
}
```

이 처럼 추상화 하면 객체가 역할에 의존하게 바꿀 수 있다.
코드 1.10
```java
class RestaurantChain implements Calculable{
    List<Calculate> stores;
    
    @Override
    public long calculateRevenue(){
	    long revenue = 0;
	    for (Calculate store : stores) {
	        revenue += store.calculateRevenue();
	    };
    };
    
    @Override
    public long calculateProfit(){...};
}

class Store implements Calculable {
    List<Calculate> orders;
    
    @Override
    public long calculateRevenue(){};
    
    @Override
    public long calculateProfit(){};
}
```

기존과 무슨 차이가 있는가?
- 기존: RestaurantChain은 반드시 Store만 가질 수 있다.
- 변경: RestaurantChain은 매출/순이익을 계산할 수 있는 대상이라면 뭐든 합산할 수 있다.
	- 구체 클래스에 의존하지 않고 "역할"에 의존하도록 설계하여 유연하게 설계 가능하다.
	- DIP(의존성 역전 원칙) 충족

> 즉, 역할은 interface를 말하며, 역할이 가진 책임은 interface 내부의 method를 말한다.
> 
> 종합해보면
> 객체 지향에서는 책임을 객체에게 할당하지 않는다.
> 객체를 추상화한 역할에 책임을 할당한다.
> (해석: 객체 지향에서는 method를 class에 할당하지 않는다. class를 추상화한 interface에 method를 할당한다.)

이로인해 다형성 구현이 가능하고 더욱 유연한 설계가 가능하다.
- 다형성의 구현방법
	1. 상속 + 오버라이딩 (extends)
	2. 인터페이스 구현 (implements)
	3. 오버로딩 (정적 다형성)
	4. 제네릭 (매개변수 다형성)
	5. 람다/함수형 인터페이스 (행위 위임 기반 다형성)

이렇게 역할을 이용해서 통신할 때 장점
- 실제 객체가 어떤 객체인지 상관 하지 않아도 된다.
- 새로운 요구사항이 생기면 그 역할을 다하는 구현체를 만들기만 하면 되므로 확장에 유연하다.

예를 들어 BrandProduct가 생기면 기존 코드의 경우 아래와 같이 같은 코드를 반복 작성해야 한다.
```java
class RestaurantChain {
    List<Store> stores;
    List<BrandProducts> brandProducts;
    
    public long calculateRevenue(){
	    long revenue = 0;
	    for (Store store : stores) {
	        revenue += store.calculateRevenue();
	    };
	    
	    for (BrandProducts brandProduct : brandProducts) {
	        revenue += brandProduct.calculateRevenue();
	    };
    };
    public long calculateProfit(){...};
}
```

그러나 객체를 추상화한 역할에 의존하면 `BrandProducts`가 `Calculabe`을 구현하기만 한다면 `코드 1.10`을 그대로 사용가능하다.

> 이렇듯 역할, 책임, 협력을 통해
> 자아를 가진 객체들이 서로 협력하는 방식으로 개발하는 것이 객체지향 패러다임의 핵심이다.


## 1.2 TDA 원칙
#DDD #비즈니스로직을도메인에 #getter #setter

> TDA(Tell, Don't Ask): 시켜라, 묻지말고.
> 즉, 객체에게서 getter로 값을 가져오지 말고, 객체가 책임을 수행하게끔 하라는 뜻

만약 `Product`가 `price`와 `quantity`를 가지고 있고, `Account`가 `mileage`를 가지고 있다면

마일리지 사용 가능 여부와 사용 후 잔액을 저장하고 싶을 때
`getter`로 `price`, `quantity`, `mileage를` 가져와서(Ask) 계산하지 말고,
`Account`에 `canAfford(long amount)`라는  책임(Method, Tell)를 만든 후 
매개변수로 `price`와 `quantity`를 곱한 `amount`를 제공하라는 뜻.

단편적으로 말하자면
무분별한 `getter`, `setter`를 줄이고 객체의 책임은 무엇일지 먼저 생각하는 객체지향적 사고를 먼저 하자.

