## 44. 표준 함수형 인터페이스를 이용하라.

- 자바 표준 라이브러리에서 같은 모양의 인터페이스를 지원한다.
- 필요에 맞는 용도가 있다면 직접 구현하지 말고, 표준 함수형 인터페이스를 활용하라.

### 💬 기본 인터페이스 6가지

: `java.util.function` 에는 총 43개의 인터페이스가 담겨있으나, 기본 6개만 기억하면 나머지는 충분히 유추 가능하다.

|인터페이스|함수 시그니처|예|
|---|---|---|
|UnaryOperator<T>|T apply(T t)|String::toLowerCase|
|BinaryOperator<T>|T apply(T t1, T t2)|BigInteger::add|
|Predicate<T>|boolean test(T t)|Collection::isEmpty|
|Function<T>|R apply(T t)|Arrays::asList|
|Supplier<T>|T get()|Instant::now|
|Consumer<T>|void accept(T t)|System.out::println|

- Operator 인터페이스
  - 인수가 1개인 `UnaryOperator`와 2개인 `BinaryOperator`로 나뉜다.
  - 반환값과 인수의 타입이 같은 함수를 뜻한다.
- Predicate 인터페이스
  - 인수 하나를 받아 `boolean`으로 반환하는 함수를 뜻한다.
- Function 인터페이스
  - 인수와 반환 타입이 다른 함수를 뜻한다.
- Supplier 인터페이스
  - 인수를 받지 않고 값을 반환(혹은 제공)하는 함수를 뜻한다.
- Consumer 인터페이스
  - 인수를 하나 받고 반환값은 없는, 인수를 소비하는 함수를 뜻한다.

→ 기본 함수형 인터페이스에는 박싱된 기본타입을 넣어 사용하지 않는다. → 성능이슈가 있을 수 있다.

### 💬 표준 함수형이 있더라도, 직접 작성해야 하는 경우

- API 에서 굉장히 자주 사용되고 지금의 이름이 그 용도를 잘 담고 있는 경우
- 구현하는 쪽에서 반드시 지켜야 할 규약을 담고 있는 경우
- 비교자들을 변환하고 조합해주는 유용한 디폴트 메소드를 담고 있는 경우

### 💬 @FuntionalInterFace

: 위와 같은 이유로 전용 함수형 인터페이스를 작성하게 된다면 `@FuntionalInterFace` 를 선언해야 한다. ( `@Override` 와 역할이 비슷하다.)

- 해당 인터페이스가 람다용으로 설계되었음을 알린다.
- 추상 메소드 오직 하나만 가지고 있어야 컴파일되게 해준다.
- 누군가 실수로 메소드를 추가하지 못하게 막아준다.

**→ 직접 만든 함수형 인터페이스에는 항상 `@FuntionalInterFace` 을 사용한다.**

### 💬 함수형 인터페이스를 API에서 사용시, 주의사항

- 메소드들을 다중정의해서는 안된다.
- 서로 다른 함수형 인터페이스를 같은 위치의 인수로 사용하는 다중정의를 피한다.