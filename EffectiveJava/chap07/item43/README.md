## 42. ****익명 클래스보다는 람다를 사용하라****

- 기존에는 익명클래스를 사용한다.

  - 하지만 해당 방식은 코드가 너무 길어서 자바는 함수형 프로그래밍에 적합하지 않다.
  - 함수형 인터페이스라 부르는 이 인터페이스들의 인스턴스를 람다식으로 만들 수 있다.

    ```java
    public class Item42 {
        public static void main(String[] args) {
            List<String> words = new ArrayList<>();
            Collections.sort(words, new Comparator<String>() {
                @Override
                public int compare(String o1, String o2) {
                    return Integer.compare(o1.length(), o2.length());
                }
            });
        }
    }
    ```

- 람다식으로 대체

    ```java
    public class Item42 {
        public static void main(String[] args) {
            List<String> words = new ArrayList<>();
            Collections.sort(words, (s1, s2) -> Integer.compare(s1.length(), s2.length()));
        }
    }
    ```

  - 여기서, 람다, 매개변수(s1, s2), 반환값의 타입은 각각 (`Comparator<String>`), `String`, `int`지만 실제 명시하지 않고 컴파일러가 타입을 추론한다.
  - **타입을 명시해야 코드가 더 명확할 때만 제외하고는, 람다의 모든 매개변수 타입은 생략한다.**
    - 컴파일러는 타입을 추론하는 데 필요한 타입 정보 대부분을 제네릭에서 얻는다.
    - 따라서 우리가 이 정보를 제공하지 않으면 컴파일러는 람다의 타입을 추론할 수 없게 되어, 결국 일일이 타입 정보를 명시해야 한다.

### 💬 람다를 인스턴스 필드에 저장해 상수별 동작을 구현하기

- 기존 enum에서의 예제 코드를 람다로 변경하기

    ```java
    public enum Operation {
        PLUS("+", (x, y) -> x + y),
        MINUS("-", (x, y) -> x - y),
        TIMES("*", (x, y) -> x * y),
        DIVIDE("/", (x, y) -> x / y);
    
        private final String symbol;
        private final DoubleBinaryOperator operator;
    
        Operation(String symbol, DoubleBinaryOperator operator) {
            this.symbol = symbol;
            this.operator = operator;
        }
    
        @Override
        public String toString() {
            return symbol;
        }
    
        public double apply(double x, double y) {
            return operator.applyAsDouble(x, y);
        }
    }
    ```

  - 기존보다 간결하게 해당 상수들의 메소드를 표현할 수 있다.

### 💬 람다 사용시 고려사항

- 람다는 이름이 없고 문서화를 못한다.
  - → 코드 자체로 동작이 명확하지 않으면 람다를 쓰지 않는다.
  - 한 줄이 베스트고, 아무리 길어도 세 줄까지 이다.
- 람다는 함수형 인터페이스에서만 쓰인다.
  - 추상클래스의 인스턴스를 만들때는 람다가 사용할 수 없으니 익명클래스를 사용해야 한다.
- 람다는 자기 자신을 참조할 수 없다.
  - → this는 바깥의 인스턴스를 말한다.
  - 함수 객체가 자기 자신을 참조하려면 반드시 익명클래스 써야 한다.

→ 익명클래스는 함수형 인터페이스가 아닌 타입의 인터페이스를 만들 때만 사용하라.