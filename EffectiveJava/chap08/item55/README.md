## 55. 옵셔널 반환은 신중히 하라.

### 🌳 값을 반환할 수 없는 경우

- 예외를 던지거나 → 진짜 예외인 경우에만 사용해야 한다.
- null을 반환한다. → 별도의 null처리가 필요하다.

### 🌳 Optional<T>

: 아무것도 담고 있지 않았다. == 비었다. 어떤 값을 가지고 있다 == 비어있지 않다.

- 변경전

    ```java
    public static <E extends Comparable<E>> E max (Collection<E> c) {
    	if(c.isEmpty())
        	throw new IllegalArgumentException("빈 컬렉션"); // 예외 발생
        
        E result = null;
        for (E e : c )
        	if (result==null || e.compareTo(result) > 0)
            	result = Objects.requireNonNull(e);
                
        return result; // null 반환
    }
    ```

    - 매개변수가 비어있으면 예외를 터뜨린다.
    - 결과 값이 null 일 수 있다.
- 변경후

    ```java
    public static <E extends Comparable<E>> Optional<E> max(Collection<E> c) {
       if (c.isEmpty())
       		return Optional.empty(); // optional
            
       E result = null;
       for (E e : c)
       		if(result==null||e.compareTo(result) > 0)
            	result = Objects.requireNonNull(e);
                
       return Optional.of(result); // optional
    }
    ```

    - `Optional`로 반환한다.
    - `Optional`로 반환할 때 `Null`을 넣어서 반환하지는 않는다. → `Optional`을 장점을 사라지게 한다.
    - `Optional.of(null)` → NullPointerException
    - `Optional.ofNullable(null)` → NPE 안터짐

→ `Optional` 은 반환값이 없을 수도 있음을 API 사용자에게 알린다.

### 🌳 Optional 사용시, 값을 받지 못했을 때 취할 행동을 선택해야 한다.

1. 기본값을 정한다.
    1. `String lastWordInLexicon = max(words).orElse("단어 없음...");`
    2. 없는 경우, 기본값을 정한다.
2. 원하는 예외를 던진다.
    1. `Toy myToy = max(toys).orElseThrow(TemperTantrumException::new);`
    2. 내가 원하는 예외를 던질 수 있다.
3. 항상 값이 채워져 있다고 가정한다.
    1. `Element lastNobleGas = max(Elements.NOBLE_GASES).get();`
    2. 값이 존재하지 않으면 `NoSuchElementException` 예외가 터진다.

기본값을 설정하는 비용이 부담이 된다면, `Supplier`를 인수로 받는 `orElseGet`을 사용하면 값이 처음 필요할 때 `Supplier`를 사용해 생성하므로 초기 설정 비용을 낮출 수 있다.

- chatGpt 예시

```java
public class SupplierOrElseGetExample {
    public static void main(String[] args) {
        Optional<String> optionalValue = Optional.empty();

        // Supplier를 인수로 받는 orElseGet을 사용하여 값이 필요할 때 Supplier에서 값을 생성
        Supplier<String> expensiveValueSupplier = SupplierOrElseGetExample::createExpensiveValue;
        String result = optionalValue.orElseGet(expensiveValueSupplier);

        System.out.println("Result: " + result);
    }

    private static String createExpensiveValue() {
        System.out.println("Creating expensive value...");
        // 복잡한 초기화 과정이나 비용이 큰 작업 수행 가능
        return "Expensive Value";
    }
}
위 코드에서 expensiveValueSupplier라는 Supplier를 생성하고, 이를 optionalValue.orElseGet(expensiveValueSupplier)로 전달하여 orElseGet 메서드를 호출하고 있습니다. orElseGet은 값이 필요한 시점에서 expensiveValueSupplier에서 값을 생성하게 됩니다. 이렇게 하면 초기 설정 비용을 필요한 시점에 낮출 수 있습니다.
```

### 🌳 isPresent, **map, filter, flatMap**

- isPresent

    - `Optional`이 채워져 있으면 `True`, 비어져있으면 `False`를 반환한다.
- map

    - `Stream<Optional>`로 받아서 그 중 채워져있는 값들만 뽑아서 Stream에 담는다.

    ```java
    System.out.println("부모 PID:" +
    	ph.parent().map(h-> String.valueOf(h.pid))).orElse("N/A");
    ```

- filter

    - `isPresent`로 존재하는 애들만 필터해서 값을 가지고 온다.

    ```java
    streamOfOptionals
    	.filter(Optional::isPresnet)
    	.map(Optional::get)
    ```

- flatMap

    - Optional을 Stream으로 변환해주는 어댑터
    - 값이 없으면 빈 스트림으로 변환한다.

    ```java
    streamOfOptionals
    	.flatMap(Optional::stream)
    ```


### 🌳 Optional을 사용하면 안되는 경우

- 컬렉션, 스트림, 배열, 옵셔널 같은 컨테이너 타입은 옵셔널로 감싸면 안 된다.
    - `빈 Optional<List>`보다는 `빈 List` 자체를 반환하는게 좋다.

### 🌳 어떤 경우 Optional<T>로 선언하는가?

: 결과가 없을 수 있으며, 클라이언트가 이 상황을 특별하게 처리해야한다면 Optional<T>로 선언한다.

### 🌳 성능이슈

- 박싱된 기본타입을 담은 옵셔널은 기본 타입 자체보다 무거울수밖에 없다.
- `OptionalInt`, `OptionalLong`, `OptionalDouble`등이 전용 옵셔널 클래스로 존재한다.
- 박싱된 기본타입을 담은 옵셔널을 반환하는 일은 없도록 하자.

### 🌳 Optional을 사용하는 않는 곳

- 옵셔널을 맵의 값으로 사용하면 절대 안 된다.
    - 맵 안에 키가 없다는 사실1 - 키 자체가 없는 경우
    - 맵 안에 키가 없다는 사실1 - 키는 존해나는데 비어있는 옵셔널인 경우

→ **옵셔널을 컬렉션의 키, 값, 원소나 배열의 원소로 사용하는 게 적절한 상황은 거의 없다.**