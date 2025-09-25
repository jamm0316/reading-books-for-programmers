|질문|저연차 답변|
|---|---|
|VO|값 객체로, 읽기 전용 객체를 말함|
|DTO|계층 간 데이터 교환에 사용되는 객체, 데이터베이스에서 데이터를 불러오고 넣을 때 사용|
|DAO|데이터베이스에 접근하는데 사용되는 객체|
|엔티티|JPA의 @Entity로, 테이블에 1:1로 대응되며, 각각 구분할 수 있는 식별자를 가지고 있음|
## 2.1 VO(Value Object, 값 객체)

말 그대로 객체로 값을 표현한 것이다.
즉, 객체이지만 값의 속성을 가지고 있어야한다는 뜻이다.

그렇다면 값이란 무엇일까?

> [!note]
> 값의 특징
> 1. 불변성: 값은 변하면 안된다.
> 2. 동등성: 값은 어디에서나 동등해야한다.
> 3. 자가 검증: 값은 신뢰할 수 있어야 한다.

예를 들어 1이란 숫자를 보자.
이 숫자은 값이다.

따라서, 내가 1을 적어둔다면 이는 종이를 아무리 구기고 다른 사람한테 넘겨주고 다시 돌려받고 전세계를 돌더라도 종이에 적힌 1은 변하면 안된다. 불변성을 갖고 있다.


또한 이 의미는 어디서나 같아야한다. 1을 가지고 대한민국에서 사과를 주문하면 사과 1개가 나와야하고, 아르헨티나에서 주문해도 사과 1개가 나와야한다. 동등성을 갖고 있다.

또한 1은 'a' 나 'ㄱ'으로 대체될 수 없다. 'a'는 알파벳이고, 'ㄱ'은 한글 자음이다. 1은 숫자이다.
또한 1은 1.01이나 0.99가 아니라 정수 1이다.
즉, 1은 자가 검증을 통해 아라비아 숫자 기호의 체계를 따르며, 그 값을 신뢰할 수 있어야한다.

### 2.1.1 불변성

> 불확실성을 없애고 '신뢰'할 수 있는 값 객체를 만들기 위한 조건

#### 모든 필드는 final 이어야한다.

```java
public class Color {
    public final int r;
    public final int g;
    public final int b;
    
    public Color (int r, int g, int b) {
        this.r = r;
        this.g = g;
        this.b = b;
    }
}
```

한 번 들어온 값은 변경되면 안되므로 final로 선언되어야 한다.

#### 만약 참조 객체가 들어온다면 그 것 또한 불변이어야 한다.

원시 타입이 아닌 **참조타입의 경우 final이어도 불변을 보장할 수 없다.**
아래 예시를 보면, Shpae는 Setter를 통해 값 변경이 가능하다.

```java
@Getter
public class Color {
    public final int r;
    public final int g;
    public final int b;
    public final Shape shape;
    
    public Color (int r, int g, int b, Shape shape) {
        this.r = r;
        this.g = g;
        this.b = b;
        this.shape = shape;
    }
}

@Data
public class Rectangle extends Shape {
    private int width;
    private int height;
    
    public Rectangle(int width, int height) {
        this.width = width;
        this.height = height;
    }
}
```


```java
Rectangle rectangle = new Rectangle(10, 20);
Color color = new Color(0, 1, 0, rectangle);

color.getShape().setWidth(20);  //값 변경 발생!!!!
```

#### 함수 또한 불변으로 순수함수를 사용해야 한다.
```java
public class Color {
    public final int r;
    public final int g;
    public final int b;
    
    public Color (int r, int g, int b) {
        this.r = r;
        this.g = g;
        this.b = b;
    }
    
    public Color randomColor() {  // 매번 다른 색깔 반환
        Random random = new Random();
        int r = random.nextInt(256);
        int g = random.nextInt(256);
        int b = random.nextInt(256);
        return new Color(r, g, b)
    }
}
```

#### 상속으로 인해 불변성이 깨지면 안된다.
```java
public class Color {
    public final int r;
    public final int g;
    public final int b;
    
    public Color (int r, int g, int b) {
        this.r = r;
        this.g = g;
        this.b = b;
    }
}

public class AlphaColor extends Color {
    public int a;  // 상속으로 클래스의 멤버변수의 불변성이 깨지는 경우
    
    public AlphaColor (int a, int r, int g, int b) {
        super(r, g, b);
        this.a = a;
    }
}
```

#### 여러개의 스레드가 접근해도 불변성은 깨지면 안된다.
```java
public class AccountInfo {
    private long mileage;
    
    public AccountLevel getLevel() {
	    switch (mileage) {
	        case (mileage > 100_000): return AccountLevel.DIAMOND;
	        case (mileage > 50_000): return AccountLevel.GOLD;
	        case (mileage > 30_000): return AccountLevel.SILVER;
	        case (mileage > 10_000): return AccountLevel.BRONZE;
	        default: AccountLevel.NONEl
	    }
    }
    
    public void setMileage (long mileage) {
        this.mileage = mileage;
    }
}
```

여러 스레드 접근으로 인해 불변성이 깨지는 경우 <br>
<img src='https://github.com/user-attachments/assets/aa3b1a9a-9520-43ee-815d-c7018776fcbc' width=700/>



따라서, 멤버변수를 불변으로 만들고, 새로운 스레드 접근 시 새로운 객체를 내보내게끔 설계해야함.
따라서, Setter를 없애고 새로운 요청이 들어오면 새로운 객체를 반환하게끔 설계해야함.

```java
@AllArgsConstructor
public class AccountInfo {
    private final long mileage;
    
    public AccountLevel getLevel() {
	    switch (mileage) {
	        case (mileage > 100_000): return AccountLevel.DIAMOND;
	        case (mileage > 50_000): return AccountLevel.GOLD;
	        case (mileage > 30_000): return AccountLevel.SILVER;
	        case (mileage > 10_000): return AccountLevel.BRONZE;
	        default: AccountLevel.NONEl
	    }
    }
    
    public AccountInfo withMileage (long mileage) {
        return new AccountInfo(this.id, mileage);
    }
}
```
<br>
<img src='https://github.com/user-attachments/assets/d4913bc9-9701-498c-9138-47df852a571c' width=700 />

#### 중요한 것은 불변성의 가치를 좇는 것!!
이처럼 불변객체를 만드는 것은 꽤나 복잡하고 생각할 거리가 많다.
소프트웨어의 불확실성을 고려할 때 완전 무결한 불변객체를 만드는 것은 어렵겠으나 불변성을 이해하고 그 가치를 좇는 노력을 해야한다.

그 이유는 단 한가지 '신뢰'할 수 있는 값을 만들기 위함이다.
내부 값이 변경되지 않는 값 객체라면 다른 객체와의 협력과정에서 예측 가능한 방식으로 동작하기 때문이다.
따라서, 불확실성과 확실성을 구분하고 확실서을 최대한 늘리려는 노력이 불변성이 추구하는 목적이다.

### 2.1.2 동등성

> 어떤 객체의 값과 상태 같다면 같은 객체로 봐야한다.

#### VO를 만들기 위해서 equals와 hashCode를 오버라이딩 할 필요가 있다.
> equals: 객체 타입 + 내부 값 비교 → 논리적 동등성
> hashCode: 숫자요약값 제공 → 해시 자료구조 성능 확보

#### VO는 식별자를 갖을 수 없다.
```java
@AllArgsConstructor
public class AccountInfo {
    private final long id;
    private final long mileage;
    
    public AccountLevel getLevel() {
	    switch (mileage) {
	        case (mileage > 100_000): return AccountLevel.DIAMOND;
	        case (mileage > 50_000): return AccountLevel.GOLD;
	        case (mileage > 30_000): return AccountLevel.SILVER;
	        case (mileage > 10_000): return AccountLevel.BRONZE;
	        default: AccountLevel.NONEl
	    }
    }
    
    public AccountInfo withMileage (long mileage) {
        return new AccountInfo(this.id, mileage);
    }
}
```

위 처럼 식별자를 갖는 경우 아래와 같은 로직이 실행된다면 어떻게 될까?
```java
AccountInfo account1 = new AccountInfo(1, 20_000);
AccountInfo account1 = account1.withMileage(70_000);
System.out.println(account1 == account2) //false
```

`account1`과 `account2`는 사실 서로 id가 같으므로 같은 객체로 판단해야한다.
그러나 이는 식별자가 생겼기 떄문에 발생하는 오류이다. 따라서 예측 불가능성이 생긴다.
즉, 식별자를 갖고 있는 객체는 VO가 될 수 없다.

### 2.1.3 자가검증

> 한번 생성된 VO의 멤버 변수에는 이상한 값이 들어올 수 없다.

자가 검증이 완벽한 객체라면 외부에서 이 객체르 사용할 때 상태에 이상한 값이 들어 있지 않을지 노심초사하지 않아도 된다.
상태 검증을 위해 if-else, try-catch문을 사용하지 않아도 된다는 것이다.

> [!important]
> VO의 목적: 신뢰할 수 있고 예측 가능한 객체를 만드는 것
>  따라서, 불변성, 동등성, 자가검증을 통해 신뢰할 수 있는 객체를 추구해야함.

## 2.2 DTO(Data Transfer Object: 데이터 전송 객체)

> [!note]
> **DTO의 목적**
> 다른 객체의 메서드를 호출하거나 시스템을 호출할 떄 매개변수를 일일이 모두 나열하는 것은 불편하므로
> 다른 객체나 시스템에 데이터를 구조적으로 만들어 전달하기 위한 객체

따라서 DTO는 객체를 오롯이 전달하는데만 집중하여 비즈니스 로직이 들어가면 안된다.

## 2.3 DAO(Data Access Object: 데이터 접근 객체)

> [!note]
> **DAO의 목적**
> 도메인 로직과 데이터베이스 연결 로직을 분리하기 위한 객체

데이터베이스와 상호작용은 데이터르 저장하고 검색하는 기술에 불과하다.
실제 애플리케이션의 핵심은 요구사항을 해결하는 비즈니스 로직이고 도메인이다.

## 2.4 엔티티(Entity: 개체)

> [!note]
> **엔티티의 3가지 종류**
> 1. **도메인 엔티티**: 도메인 모델 중 식별 가능하고 비즈니스 로직을 갖고 있음.
> 2. **DB 엔티티**: 데이터로 표현하려는 대상
> 3. **JPA 엔티티**: @Entity 애너테이션이 적용된 영속성 객체

### 2.4.1 도메인 엔티티

은행에서 사용하는 소프트웨어를 만들 경우

- 도메인: 은행
- 도메인 모델: Account, Transaction, Money

이 중 Account와 Transaction의 경우 아래와 같은 속성 덕분에 도메인 모델 중에서도 **도메인 엔티티라**고 부른다.
1. 비즈니스 로직을 갖을 수 있다.
2. 생애주기를 갖을 수 있다.

즉, 도메인 엔티티는 다음과 같은 속성을 갖는다.
1. 식별자를 갖는다.
2. 비즈니스 로직을 갖는다.

**즉, 도메인 엔티티는 식별이 가능하고 비즈니스 로직을 갖고 있는 객체이다.**

### 2.4.2 DB 엔티티

- 데이터베이스 분야에서 사용되던 용어로 유무형의 객체를 표헌하기 위한 수단

### 2.4.3 JPA 엔티티

- JPA의 @Entity애너테이션이 적용된 '영속성 객체'
- JPA엔티티는 DB엔티티에 뿌리를 두고있음.

이처럼 불변객체를 만드는 것은 꽤나 복잡하고 생각할 거리가 많다.
소프트웨어의 불확실성을 고려할 때 완전 무결한 불변객체를 만드는 것은 어렵겠으나 불변성을 이해하고 그 가치를 좇는 노력을 해야한다.

그 이유는 단 한가지 '신뢰'할 수 있는 값을 만들기 위함이다.
내부 값이 변경되지 않는 값 객체라면 다른 객체와의 협력과정에서 예측 가능한 방식으로 동작하기 때문이다.
따라서, 불확실성과 확실성을 구분하고 확실서을 최대한 늘리려는 노력이 불변성이 추구하는 목적이다.
