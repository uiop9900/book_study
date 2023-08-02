## 26. 로(raw) 타입은 사용하지 말라.

### 💡 제네릭 타입이란?

: 제네릭 클래스와 제네릭 인터페이스를 통틀어서 칭한다.

- 제네릭 클래스/인터페이스 : `클래스`와 `인터페이스` 선언에 타입 매개변수가 쓰이는 걸 뜻한다.

- 예시

    ```java
    public class ClassName<T> { ... }
    public interface InterfaceName<T> { ... }
    ```

- 제네릭 타입은 일련의 매개변수화 타입을 정의한다.

  - 꺽쇠 안에 실제 타입을 나열한다.
  - `List<String>` : String이 실제 타입 매개변수로 String인 리스트를 뜩하는 매개변수화 타입이다.
- 제네릭 타입을 하나 정의하면 그에 딸린 로(raw) 타입도 함께 정의된다.

  - 로(raw) 타입은 제네릭 타입에서 타입 매개변수를 전혀 사용하지 않을때를 말한다.
  - List<E> 의 타입은 List인데, 제네릭 타입 정보가 전부 지워진 것 처럼 동작한다.
- raw 타입으로 선언한 예시

    ```java
    public class StampCollection {
        private final Collection stamps = ...;
    
        public static void main(String[] args) {
        StampCollection stamps = new StampCollection();
        // 실수로 동전을 넣는다.
        stamps.add(new Coin(...)); // "unchecked call" 경고를 내뱉는다.
    
        for (Iterator i = stamps.iterator(); i.hasNext();) {
            Stamp stamp = (Stamp) i.next(); // ClassCastException을 던진다.
            stamp.cancel();
        }
    }
    ```

  - 처음부터 `private final Collection<Stamp> stamps = ...;` 로 선언했으면 컴파일 시점에 에러가 난다.

### 💡 raw 타입을 사용하면 제네릭이 안겨주는 안정성, 표현력을 모두 잃게 된다.

- List<Object>는 사용해도 된다. → 모든 타입을 허용한다는 의미
- List list 는 안된다. → 값을 꺼낼때 에러가 발생한다.
- 제네릭 타입을 쓰고 싶으면서 매개변수가 무엇인지 신경쓰고 싶지 않을때는 <?>를 사용한다.
- Set<?>과 Set의 차이
  - 와일드카드는 안전하고 로 타입은 안전하지 않다.
  - Set에는 아무거나 넣을 수 있어서 타입 불변식을 훼손하기 쉽다.
  - <?>를 사용하면 (null외에는) 어떤 원소도 넣을 수 없다. → 넣으면 컴파일 시 에러가 난다.

### 💡 예외

- class 리터럴에는 로 타입을 사용해야 한다.

  - 허용
    - `List.class`, `String[].class`, `int.class`
  - 비허용
    - `List<String>.class`, `List<?>.class`
- `instanceOf` 메소드에는 로 타입, 비한정적 와일드 카드도 다 동일하게 작동한다.

    ```java
    if (o instanceof Set) {
        Set<?> s = (Set<?>) o;
        ...
    }
    ```

  - 타입을 확인한 후에 형변환을 한다.