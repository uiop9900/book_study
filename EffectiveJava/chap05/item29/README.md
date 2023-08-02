## 29. 이왕이면 제네릭 타입으로 만들라.

: 제네릭 타입을 새로 만드는 일은 어렵지만 배워둘만 하다.

### 💡 일반클래스를 제네릭 클래스로 만들기

- 일반 클래스

    ```java
    public class Stack {
        private Object[] elements;
        ...
    
        public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
        }
    
        public void push(Object e) {
        ...
        }
    
        public Object pop() {
        if (size == 0)
            throw new EmptyStackException();
        Object result = elements[--size];
        ...
        }
        ...
    }
    ```

- 제네릭 클래스

    ```java
    public class Stack<E> {
        private E[] elements;
        ...
    
        public Stack() {
        elements = (E[]) new Object[DEFAULT_INITIAL_CAPACITY];
        }
    
        public void push(E e) {
        ...
        }
    
        public E pop() {
        if (size == 0)
            throw new EmptyStackException();
        E result = elements[--size];
        ...
        }
        ...
    }
    ```


1. 클래스 선언에 타입 매개변수`<E>`를 추가한다.
2. public class Stack {…} → public class Stack<E> {…}

→ E와 같은 실체화 불가 타입으로는 배열을 만들 수 없다.

### 💡 1. 제네릭 배열 생성을 금지하고 우회한다.

위의 제네릭 클래스의 Object 배열을 만든 다음 제네릭 배열로 형변환한다.

- 에러메세지

    ```java
    Stack.java:8: warning: [unchecked] unchecked cast
    found: Object[], required: E[]
            elements = (E[]) new Object[DEFAULT_INITIAL_CAPACITY];
                            ^
    ```

- 확실히 안전하다고 판단 → @SuppressWarnings("unchecked") 로 선언한다.

    ```java
    // 배열 elements는 push(E)로 넘어온 E 인스턴스만 담는다.
    // 따라서 타입 안전성을 보장하지만,
    // 이 배열의 런타임 타입은 E[] 가 아닌 Object[]다!
    @SuppressWarnings("unckecked")
    public Stack() {
        elements = (E[]) new Object[DEFAULT_INITIAL_CAPACITY];
    }
    ```


### 💡 2. elements 타입을 `E[]` → `Object[]`로 변환한다.

```java
public class StackGeneric<E> {
    private Object[] elements; // Obejct[] 타입이다!
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public StackGeneric() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public E pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }

        @SuppressWarnings("unchecked") // push에서 E 타입만 허용하므로 이 형변환은 안전하다.
        E result = (E) elements[--size];

        elements[size] = null; // 다 쓴 참조 해제
        return result;
    }

    public boolean isEmpty() {
        return size == 0;
    }

    private void ensureCapacity() {
        if (elements.length == size) {
            elements = Arrays.copyOf(elements, 2 * size + 1);
        }
    }
}
```

- 위의 방법과 동일하게 직접 증명하고 경고를 숨긴다.

- 첫번째 방법

  - 가독성이 좋다.
    - 배열을 E[]로 선언하고 E로 받는다.
  - 코드도 더 짧다.
  - 배열 생성히 한번만 형변환 해주면 된다.
  - 힙 오염이 될 수
- 두번째 방법

  - 매번 형변환 해줘야 한다.

### 💡 대다수의 제네릭 타입은 타입 매개변수에 아무런 제약을 두지 않는다.

- `Stack<Object>`, `Stack<int[]>`, `Stack<List<String>>`, `Stack`등 어떤 참조로도 Stack을 만들 수 있다.
- `Stack<int>`, `String<double>` 와 같은 기본타입은 사용할 수 없다.
- `Stack<E extends Delayed>` 로 해서 하위타입만 받는 식으로도 선언이 가능하다.
- 모든 타입은 자기 자신의 하위타입이므로 이와 같이 사용이 가능하다. `DelayQueue<Delayed>`