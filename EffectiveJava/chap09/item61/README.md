## 61. 박싱된 기본 타입보다는 기본 타입을 사용하라.

### 🎃 자바는 기본 타입과 참조 타입으로 이루어져 있다.

- 기본타입

  - int, double, boolean …
  - String, List …
- 박싱된 기본 타입

  - Integer, Double, Boolean
- 오토박싱과 오토언박싱 덕에 두 타입을 구분하지 않고 사용이 가능하다.

- 하지만 차이는 분명히 있다.


### 🎃 기본타입과 박싱된 기본타입의 차이점

- `기본 타입`은 값만 가지고 있고, `박싱된 기본타입`은 값과 식별성을 갖는다.
  - `박싱된 기본타입`의 두 인스턴스는 값이 같아도 서로 다르다고 식별될 수 있다.
- `박싱된 기본타입`은 null을 허용한다.
- `기본타입`이 `박싱된 기본타입`에 비해 시간과 메모리 사용면에서 효율적이다.

### 🎃 예시코드 (1)

```java
Comparator<Integer> naturalOrder = 
    (i, j) -> (i < j) ? -1 : (i == j ? 0 : 1);
```

- (i < j) 는 정상적으로 작동한다.
  - Integer가 기본 타입 값으로 변환된다.
- i == j 에서 0 이 아닌 -1를 내뱉는다.
  - → 두 객체의 식별성을 검사한다.
  - naturalOrder.compare(new Integer(42), new Integer(42))
  - 서로 다른 Integer 객체는 다른 값으로 판단한다.
  - 박싱된 기본 타입에 == 을 사용하면 오류가 일어난다.
- 기본 타입을 다루는 비교자가 필요하다면 `Comparator.naturalOrder()`를 사용하자.

→ `compare`로 문제를 해결하고 싶다면, 기본 타입 변수로 변경한 다음에 수행한다.

```java
Comparator<Integer> naturalOrder = (iBoxed, jBoxed) -> {
    int i = iBoxed, j = jBoxed; // 오토 박싱
    return i < j ? -1 : (i == j ? 0 : 1);
};
```

### 🎃 예시코드 (2)

```java
public class Unbelievable {
    static Integer i;
 
    public static void main(String[] args) {
        if (i == 42) // 여기서 NPE가 터진다.
            System.out.println("믿을 수 없군!");
    }
}
```

: 결과로 `NullPointException`을 내뱉는다.

- `i`는 `Integer`고 비교값인 `42`는 `int` 이다.
- 기본타입과 박싱된 기본타입을 혼용한 연산에서는 박싱된 기본타입의 박싱이 자동으로 풀린다.
- `Integer → int`가 되는데, 이때 `int`의 초기값인 `null` 할당되어서 `NPE`가 터진다.

→ i 를 int로 변환해주면 정상적으로 작동된다.

### 🎃 예시코드 (3)

```java
public static void main(string[] args) {
    Long sum = 0L;
    for (long i = 0; i <= Integer.MAX_VALUE; i++) {
        sum += i;
    }
    System.out.println(sum);
}
```

: `sum`은 `Long`이고, `i`는 `long`으로 선언되어서 박싱과 언박싱이 반복적으로 일어나 체감될 정도로 성능이 느려진다.

### 🎃 박싱된 기본 타입은 언제 사용할까?

- 컬렉션의 원소, 키, 값으로 쓴다.
- `매개변수화 타입`이나 `매개변수화 메소드`의 `타입 매개변수`로는 `박싱된 기본 타입`을 사용한다.
  - `ThreadLocal<int>` → `ThreadLocal<Integer>`
- 리플렉션을 통해 메소드를 호출할 때도, `박싱된 기본타입`을 사용한다.