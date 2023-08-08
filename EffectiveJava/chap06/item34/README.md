## 34. int 상수 대신 열거 타입을 사용하라.

- 열거타입이란? 일정 개수의 상수값을 정의하고 그 외의 값은 허용하지 않는다.

### 🕹️ 정수형 열거패턴과 단점

```java
public static final int APPLE_FUJI = 0;
public static final int APPLE_PIPPIN = 1;
public static final int APPLE_GRANNY_SMITH = 2;

public static final int ORANGE_NAVEL = 0;
public static final int ORANGE_TEMPLE = 1;
public static final int ORANGE_BLOOD = 2;
```

- 타입 안전을 보장할 수 없고 표현력도 좋지 않다.
- 별도의 이름공간을 지원하지 않음 → 동일이름은 충돌이 난다.
  - ex) MERCURY(수은), MERCURY(수성)
- 상수의 값이 변경되면 다시 컴파일 해야한다.
- 문자열로 출력하기 까다롭다. → 문자열 상수를 사용하는 변형 패턴 → 하드코딩으로 기능저하

### 🕹️ 열거타입과 특징

```java
public enum Apple {FUJI, PIPPIN, GRANNY_SMITH}
public enum Orange {NAVEL, TEMPLE, BLOOD}
```

- 열거타입은 완전한 형태의 클래스로 상수 하나당 인스턴스를 만들어서 `public static final`로 제공한다.
- 열거타입으로 생성된 인스턴스들은 딱 하나만 존재한다. → 열거타입은 싱글턴을 일반화한 형태이다.
- 컴파일타임 타입 안전성을 제공한다.
  - 해당 열거타입에 대한 변수는 예측이 가능하기 때문에 다른 값을 넘기면 에러가 난다.
- 각자의 이름공간이 있어서 동일 이름 상수도 가능하다.
- 임의의 메소드를 추가할 수 있다.

### 🕹️ 데이터와 메서드를 갖는 열거타입

```java
@Getter
public enum Planet {
    MERCURY(3.302e+23, 2.439e6),
    VENUS(4.869e+24, 6.052e6),
    EARTH(5.975e+24, 6.378e6),
    MARS(6.419e+23, 3.393e6),
    JUPITER(1.899e+27, 7.149e7),
    SATURN(5.685e+26, 6.027e7),
    URANUS(8.683e+25, 2.556e7),
    NEPTUNE(1.024e+26, 2.447e7);
    
    private final double mass;            // 질량(단위: 킬로그램)
    private final double radius;          // 반지름(단위: 미터)
    private final double surfaceGravity;  // 표면중력(단위: m / s^2)
    
    // 중력상수 (단위: m^3 / kg s^2)
    private static final double G = 6.67300E-11;
    
    // 생성자
    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
        this.surfaceGravity = G * mass / (radius * radius);
    }
    
    public double surfaceWeight(double mass) {
        return mass * surfaceGravity;
    }
}
```

- 열거 타입 상수 각각을 특정 데이터와 연결하기 위해서는 생성자에서 데이터를 받아서 인스턴스 필드에 저장하면 된다.
- 열거 타입은 근본적으로 불변이라 모든 필드는 final이어야 한다.
- 필드는 private으로 두고, 별도의 public 접근자 메소드를 두는 게 낫다.

### 🕹️ 상수를 제거한다면?

: 해당 상수를 참조하던 것들만 컴파일시 에러가 나고 그 외에는 아무런 영향이 없다.

- 이런 열거 타입 상수는 `클래스`이기때문에, 노출해야할 합당한 이유가 없다면 `private`으로, 필요하다면 `package-private`으로 선언한다.

### 🕹️ 각 각의 상수마다 동작이 달라져야 하는 상황에서는?

: switch문 보다는 apply 추상메소드를 선언해 상수별 메소드 구현을 한다.

- switch문

    ```java
    /* 상수가 뜻하는 연산을 수행한다. */
    public double apply(double x, double y) {
        switch(this) {
            case PLUS: return x + y;
            case MINUS: return x - y;
            case TIMES: return x * y;
            case DIVIDE: return x / y;
        }
        throw new AssertionError("알 수 없는 연산: " + this);
    }
    ```

- apply문 → **상수별 메소드 구현**

    ```java
    public enum Operation {
        PLUS {public double apply(double x, double y) {return x + y;}},
        MINUS {public double apply(double x, double y) {return x + y;}},
        TIMES {public double apply(double x, double y) {return x + y;}},
        DIVIDE {public double apply(double x, double y) {return x + y;}};
        
        public abstract double apply(double x, double y);
    }
    ```


### 🕹️ 상수별로 동작이 달라져야 하는 상황에서는?

- 열거타입 상수끼리 코드를 공유하기 어렵다.
  - 기존코드

      ```java
      enum PayrollDay {
          MONDAY, TUESDAY, WEDSDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY;
          
          private static final int MINS_PER_SHIFT = 8 * 60;
          
          int pay(int minutesWorked, int payRate) {
              int basePay = minutesWorked * payRate;
              
              int overtimePay;
              switch(this) {
                  case SATURDAY: case SUNDAY: // 주말
                      overtimePay = basePay / 2;
                      break;
                  default: // 주중
                      overtimePay = minutesWOrked <= MINS_PER_SHIFT ?
                      0 : minutesWorked - MINS_PER_SHIFT) * payRate / 2;
              }
                  
              return basePay + overtimePay;
          }
      }
      ```


→ 새로운 휴가(상수)가 생기면 case문을 새롭게 넣어줘야 한다.

- 보완된 코드

    ```java
    enum PayrollDay {
        MONDAY, TUESDAY, WEDSDAY, THURSDAY, FRIDAY, 
        SATURDAY(PayType.WEEKEND), SUNDAY(PayType.WEEKEND);
        
        private final PayType payType;
        
        PayrollDya(PayType payTyoe) {this.payType = payType;}
        
        int pay(int minutesWorked, int payRate) {
        	return payType.pay(minutesWorked, payRate);
        }
        
        /* 전략 열거 타입 */
        enum PayType {
            WEEKDAY {
                int overtimePay(int minusWorked, int payRate) {
                    return minusWorked <= MINS_PER_SHIFT ? 0 :
                    (minusWorked - MINS_PER_SHIFT) * payRate / 2;
                }
            },
            WEEKEND {
                int overtimePay(int minusWorked, int payRate) {
                    return minusWorked * payRate / 2;
                }
            };
            
            abstract int overtimePay(int mins, int payRate);
            private static final int MINS_PER_SHIFT = 8 * 60;
            
            int pay(int minsWorked, int payRate) {
                int basePay = minsWorked * payRate;
                return basePay + overtimePay(minsWorked, payRate);
            }
        }
    }
    ```


→ 전략도 열거방식으로 선언해서 해당 전략일때 다른 계산 로직을 타게끔 한다.

### 🕹️ 지원하는 메소드들

- `values()` : 자신 안에 정의된 상수들의 값을 배열에 담아 반환하는정적 메서드, 값들은 선언된 순서로 저장
- `valueOf()` : 상수 이름을 입력받아 그 이름에 해당하는 상수를 반환
- `toString()` : 상수 이름을 문자열로 반환, 원하는 대로 재정의도 가능하다.
- `fromString()` : `toString`이 반환하는 문자열을 해당 열거 타입 상수로 변환

### 🕹️ 정리

- 기존 열거타입에 상수별 동작을 혼합해 넣을때는 Switch문이 좋은 선택일 수 있다.
- 필요한 원소르 컴파일타임에 다 알 수 있는 상수 집합이라면 항상 열거 타입을 사용하자.
- 열거 타입에 정의된 상수의 개수가 영원히 고정 불변일 필요는 없다.