## 32. 제네릭과 가변인수를 함께 쓸 때는 신중하라.

: 가변인수는 인수의 개수를 클라리언트가 조절할 수 있게 해준다.

가변인수 메소드를 호출하면 가변인수를 담기 위한 배열이 자동으로 하나 만들어진다.

- 매개변수화 타입의 변수가 타입이 다른 객체를 참조하면 힙 오염이 발생한다.

```java
static void danferous(List<String>... stringLists) {
    List<Integer> intList = List.of(42);
    Object[] objects = stringLists;
    objects[0] = intList; // 힙 오염 발생
    String s = stringLists[0].get(0); // ClassCastException
}
```

→ 제네릭 varargs 배열에 매개변수를 저장하는 것은 안전하지 않다.

### 💡 제네릭 varargs 매개변수를 받는 메서드를 선언할 수 있게 한 이유는?

- 제네릭 배열을 프로그래머가 직접 생성하는 건 허용하지 않는다.
- `제네릭`이나 `매개변수화` 타입의 varargs 매개변수를 받는 메서드가 실무에서 매우 유용하기 때문이다.
- 사실 자바 라이브러리도 이런 메서드를 제공한다.
- 예시
  - `Arrays.asList(T... a)`, `Collections.addAll(Collection<? super T> c`, `T... elements)`, `EnumSet.of(E first, E...rest)`가 대표적이다.

### 💡 메서드가 안전한지 어떻게 확인하는가?

- varargs 매개변수 배열에 아무것도 저장하지 않는다.
- 그 배열(혹은 복제본)을 신뢰할 수 없는 코드에 노출하지 않는다.
- `@SafeVarargs` 은 그 메소드가 타입 안전함을 보장하는 장치이다.
  - varargs가 순수하게 인수들을 전달하는 일만 한다.
- varargs 매개변수 배열에 다른 메소드가 접근하도록 허용하면 안전하지 않다.
  - `@SafeVarargs` 로 된 또 다른 varargs함수에 넘기는 건 안전하다.