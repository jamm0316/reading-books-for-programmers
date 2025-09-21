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

> [!note] 값의 특징
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
