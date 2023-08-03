## 28. 배열보다는 리스트를 사용하라.

### 💡 배열은 공변하지만 제네릭은 불공변이다.

- 공변: 같이 변한다.
  - 배열
    - `Sub`이 `Super`의 하위타입이라면,
    - `List<Sub>`도 `List<Super>`의 하위타입이다.
  - 제네릭
    - `Type1`이 `Type2`의 하위타입이라도
    - `List<Type1>` 과 `List<Type2>` 이 상관없다.
  - 배열은 실수를 런타임이 되어야 알 수 있고, 리스트는 컴파일할때 바로 알 수 있다.

### 💡 배열은 실체화된다.

- 실체화란?
  - 런타임에도 자신이 담기로 한 원소의 타입을 인지하고 확인한다는 뜻이다.
- 배열: `Object[]` → 실체화 된다.
- 리스트: `List<Object>`
- 배열은 런타임에도 원소의 타입을 인지하고 확인하지만 제네릭은 컴파일타임에만 검사하고 런타임시점에는 소거 된다.
  - → 배열과 제네릭은 잘 어우러지지 않는다.

### 💡 배열은 제네릭타입, 매개변수화타입, 타입 매개변수를 사용할 수 없다.

: `new List<E>[]`, `new List<String>[]`, `new E[]` 로 작성할 수 없다.

- 제네릭타입이 안전하지 않기때문에 허용하지 않는다.
- 이는 런타임에 위 예외가 발생하는 일을 막아주겠다는 제네릭 타입 시스템의 취지에 어긋난다.
- 타입이 안전하지 않다.
  - `ClassCastException`이 발생한다.

### 💡 예시

```java
public class Chooser {
    private final Object[] choiceArray;

    public Chooser(Collection choices) {
        choiceArray = choices.toArray();
    }

    public Object choose() {
        Random rnd = ThreadLocalRandom.current();
        return choiceArray[rnd.nextInt(choiceArray.length)];
    }
}
```

- `choose()`를 호출할 때마다 반환된 Object를 원하는 타입으로 형변환해야한다. 이를 제네릭을 사용해 개선해보자.

```java
public class Chooser<T> {
    private final T[] choiceArray; // Object -> T[]

    public Chooser(Collection<T> choices) {
        choiceArray = (T[]) choices.toArray(); // 형변환 경고 발생
    }
    ...
}
```

- 이렇게 수정하면 형변환을 할 필요가 없어진다. 다만, 형변환 경고가 발생한다. List를 사용해 형변환 경고를 없애보자.

```java
public class Chooser<T> {
    private final List<T> choiceList; // list로 변경

    public Chooser(Collection<T> choices) {
        choiceList = new ArrayList<>(choices);
    }

    ...
}
```

