## 31. 한정적 와일드카드를 사용해 API 유연성을 높이라.

- 매개변수화 타입은 불공변이다.
  - `List<String>` 는 `List<Object>`의 하위타입이 아니다.
  - `List<String>`는 `List<Object>`가 하는 일을 제대로 수행하지 못하니 하위타입이 될 수 없다.

```java
public class Stack<E> {
    public Stack();
    public void push(E e);
    public E pop();
    public boolean isEmpty();
    public void pushAll(Iterable<E> src) {
    for (E e : src)
        push(e);
    }
}
```

→ 컴파일은 되지만 완벽하지 않다.

### 💡`Iterable<? extends E>`

- 예시

    ```java
    Strack<Number> numberStack = new Stack<>();
    Iterable<Integer> intergers = ...;
    numberStack.pushAll(integers);
    ```

  - Stack을 `Number`로 선언하고 `Integer`를 넣었다.
  - 잘될 것으로 예상하지만 실제로는 컴파일에러가 난다. (매개변수화는 불공변이기 때문이다.)
- `pushAll` 의 입력매개변수는 `Iterable<E>` 가 아니라, `Iterable<? extends E>` 를 선언해야 한다.


### 💡`Collection<? super E>`

- 예시

    ```java
    public void popAll(Collection<E> dst) {
        when (!isEmpty())
        dst.add(pop())
    }
    ```

  - `popAll` 의 입력매개변수는 Collection<E> 가 아니라, `Collection<? super E>` 를 선언해야 한다.

### 💡 팩스(PECS) : producer-extends, consumer-super 공식

: 와일드 카드를 사용하는 기본 원칙이다.

- 매개변수화 타입 T 가 생산자라면?

  - `<? extends T>` 를 사용한다.
- 매개변수화 타입 T 가 소비자라면?

  - `<? super T>` 를 사용한다.
- 반환타입에는 와일드카드를 사용하지 않는다.

- 예시

    ```java
    // 기존
    public static <E> Set<E> union(Set<E> s1, Set<E> s2)
    // 공식 따르기
    public static <E> Set<E> union(Set<? extends E> s1, Set<? extends E> s2)
    ```


→ 클래스 사용자가 와일드카드 타입을 신경써야 한다면 그 API는 무슨 문제가 있을 가능성이 크다.

### 💡 타입 매개변수와 와일드카드 중 어느 것을 사용하는 게 좋을까?

```java
public static <E> void swap(List<E> list, int i, int j); // 타입매개변수
public static void swap(List<?> list, int i, int j); // 와일드카드
```

- 타입 매개변수가 한 번만 나오면 와일드 카드로 대체하라.
- `Comparable`은 언제나 소비자이기때문에 `Comparable<E>` 보다는 `Comparable<? extends E>` 를 사용한다.

### 💡 정리

```
조금 복잡하더라도 와일드카드 타입을 적용하면 API가 훨씬 유연해진다. 
그러니 널리 쓰일 라이브러리를 작성한다면 반드시 와일드카드 타입을 적절히 사용해줘야 한다. 
PECS 공식을 기억하자. 즉, 생산자(producer)는 extends를 소비자(consumer)는 super를 사용한다. 
Comparable과 Comparator는 모두 소비자라는 사실도 잊지 말자
```
