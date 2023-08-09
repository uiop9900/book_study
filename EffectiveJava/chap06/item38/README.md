## 38. 확장할 수 있는 열거 타입이 필요하면 인터페이스를 사용하라.

### 🕹️ 타입 안전 열거 패턴

: 타입 안전 열거 패턴은 열거한 값들을 그대로 가져온 다음, 값을 더 추가할 수 있다. → 확장이 가능하다.

```java
public class DSymbolType{
  private final String type;
  private DSymbolType(String type){
    this.type = type;
  }
  public String toString(){
    return type;
  }
  public static final DSymbolType Terminal = new DSymbolType("Terminal");
  public static final DSymbolType Process = new DSymbolType("Process");
  public static final DSymbolType Decision = new DSymbolType("Decision");
}
```

### 🕹️ 열거 타입

: 확장이 불가하다.

- 확장할 수 있는 열거타입은 연산코드에만 어울린다.
- 열거타입은 임의의 인터페이스를 구현할 수 있다.

### 🕹️ 예시

```java
public interface Operation {
    double apply(double x, double y);
}

// Operation을 구현함
public enum BasicOperation implements Operation {
    PLUS("+") {
        public double apply(double x, double y) { return x + y; }
    },
    MINUS("-") {
        public double apply(double x, double y) { return x - y; }
    },
    TIMES("*") {
        public double apply(double x, double y) { return x * y; }
    },
    DIVIDE("/") {
        public double apply(double x, double y) { return x / y; }
    };
    private final String symbol;
    BasicOperation(String symbol) {
        this.symbol = symbol;
    }
    @Override public String toString() {
        return symbol;
    }
}
```

- 또 다르게 연산 타입을 확장한다.

```java

public enum ExtendedOperation implements Operation {
    EXP("^") {
        public double apply(double x, double y) {
            return Math.pow(x, y);
        }
    },
    REMAINDER("%") {
        public double apply(double x, double y) {
            return x % y;
        }
    };
    private final String symbol;
    ExtendedOperation(String symbol) {
        this.symbol = symbol;
    }
    @Override public String toString() {
        return symbol;
    }
}
```

```java
public static void main(String[] args) {
    double x = Double.parseDouble(args[0]);
    double y = Double.parseDouble(args[1]);
		// 확장된 연산을 알려준다.
    test(Arrays.asList(ExtendedOperation.values()), x, y);
}
private static void test(Collection<? extends Operation> opSet,
                         double x, double y) {
    for (Operation op : opSet)
        System.out.printf("%f %s %f = %f%n",
                x, op, y, op.apply(x, y));
}
```

### 🕹️ 정리

- 열거 타입끼리 구현을 상속할 수 없다.
- 그래서 공유하는 것들이 많다면, 별도의 메소드로 분리해서 코드중복을 없앤다.