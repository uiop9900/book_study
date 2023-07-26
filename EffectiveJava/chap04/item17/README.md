## 17. 변경가능성을 최소화하라.

### 💎 불변 클래스란?

: 인스턴스 내부 값을 변경할 수 없는 클래스로, 한번 고정된 값으로 파괴될때까지 해당 객체는 그 값을 갖는다.

- 가변 클래스보다 설계, 구현, 사용하기 쉬우며 오류가 생길 여지도 적다.
- 예) `String`, `박싱클래스`, `BigInteger`, `BigDecimal`

### 💎 클래스를 불변으로 만들기 위한 5가지 규칙

- 객체의 상태를 변경하는 메소드(변경자)를 제공하지 않는다.
- 클래스를 확장할 수 없도록 한다.
    - 상속을 막는 대표적인 방법 : `final` 로 선언한다.
- 모든 필드를 `final`로 선언한다.
- 모든 필드를 `private`로 선언한다.
    - `public final` 로 선언해도 불변객체가 되지만, 내부 표현을 바꿀 수 없어서 추천하지 않는다.
- 자신 외에는 내부의 가변 컴포넌트에 접근하지 못하게 한다.

### 💎  함수형 프로그래밍

: 피연산자를 받아서 결과에 반영하지만, 피연산자 자체는 변경되지 않고 그대로다.

- 예시

    ```java
    public final class Complex {
    	private final double re;
    	private final double im;
    
    	public Complex(double re, double im) {
    		this.re = re;
    		this.im = im;
    	}
    
    	public Complex plus(Complex c) {
    		return new Complex(re + c.re, im +c.im);
    	}
    
    }
    ```

    - `plus` 메소드는 객체를 받아서 새롭게 객체 생성을 한 후 반환한다.
- 절차적(명령형)프로그래밍
    - 피연산자인 본인을 수정해서 값이 변경된다.

### 💎 불변객체는 단순하다.

- 불변객체는 thread-safe해서 따로 동기화할 필요가 없다.
- 여러 쓰레드가 동시에 사용해도 훼손되지 않는다. → 안심하고 공유할 수 있다.
- 불변 클래스는 한번 생성해서 계속해서 재활용한다.
- 불변클래스는 clone 이나 복사는 하지않는게 좋다. 어차피 복사해도 동일객체라 의미가 없다.
- 불변클래스는 자주 사용되는 인스턴스는 중복 생성하지 않게 정적팩토리를 제공한다.

### 💎 불변객체는 불변객체끼리 내부데이터를 공유할 수 있다.

```java
public class BigInteger extends ... {
    final int signum;
    final int[] mag;
    // ... 생략
    public BigInteger negate() {
        return new BigInteger(this.mag, -this.signum);
    }
```

- `negate`메소드는 `mag` 값을 그대로 사용하고 있다. → 불변객체라 그대로 사용한다.
- 객체 생성시 다른 불변객체들을 구성요소로 사용하면 이점이 많다.
    - 구조가 아무리 복잡하더라도 불변식을 유지하기 훨씬 수월하다.
        - 예) Map의 key 혹은 Set
- 불변객체는 실패원자성을 제공한다.
    - 상태가 변하지 않으니 불일치 상태에 빠질일이 없다.
    - 실패원자성: 예외가 발생해도 그 객체는 여전히 유효해야한다.

### 💎 불변객체의 단점

- 값이 달라지면 새롭게 객체를 생성해야한다.
    - 가변동반 클래스를 제공한다.
        - 미리 예측해 다단계 연산들은 `private package`로  기본 제공한다.
    - 예측이 불가하다면,
        - 불변클래스를 public으로 제공한다.
        - 예) 불변객체- `String`, 가변동반클래스- `StringBuilder`

### 💎 불변유지를 위해 상속을 못하게 하려면?

```java
public class Complex {
	private final double re;
	private final double im;

	 private Complex(double re, double im) { // 생성자를 Private로 선언
		this.re = re;
		this.im = im;
	}

	public static Complex valueOf(double re, double im) {
		return new Complex(re, im);
	}

}
```

- 모든 생성자를 `private` 혹은 `package-private`로 만들고
- `public` 정적메소드를 만든다.

### 💎 정리

- getter가 있다고해서 setter도 있을필요는 없다.
- 꼭 필요한 경우가 아니면 불변으로 짠다.
- 변경할수있는 부분은 최소한으로 줄인다.
- 다른 특별한 이유가 있지않으면 필드는 `private final`로 선언한다.
- 불변식 설정이 완료된, 초기화가 된 상태에서 객체를 생성한다.