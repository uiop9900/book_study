## 27. 비검사 경고를 제거하라.

제네릭을 사용하면 많은 컴파일러 경고를 보게된다.

- `비검사형변환 경고`, `비검사 메소드 호출 경고`, `비검사 매개변수화 가변인수 타입 경고`, `비검사 변환 경고` 등

### 💡 한번에 깔끔하게 컴파일 될 것을 기대하지 말아라.

- 예시

    ```java
    Set<Lark> exaltation = new HashSet();
    
    Venery.java:4: warning: [unchecked] unchecked conversion
        Set<Lark> exaltation = new HashSet();
                                ^
    ```

  - 컴파일 경고대로 수정

    ```java
    Set<Lark> exaltation = new HashSet<>();
    ```


→ 할 수 있는 한 모든 비검사 경고를 제거한다.

### 💡 @SuppressWarnings("unchecked")

: 경고를 제거할 수는 없지만, 타입이 안전하다고 확신한다면 어노테이션을 통해 경고를 제거한다.

- 항상 가능한 한 좁은 범위에 적용한다.

- 한 줄이 넘는 메소드나 생성자에 달지 않고 지역변수 선언쪽에 해당 어노테이션을 선언한다.

    ```java
    public <T> T[] toArray(T[] a) {
        if(a.length < size) {
        // 생성한 배열과 매개변수로 받은 배열의 타입이 모두 T[]로 같으므로
        // 올바른 형변환이다.
        @SuppressWarnings("unchecked") T[] result =
            (T[]) Arrays.copyOf(elements, size, a.getClass());
            return result;
        }
        ...
    }
    ```

  - 메소드 전체에 하지 않고, 반환되는 값이 `result(지역변수)` 에만 해당 어노테이션을 단다.
- 그 경고를 무시해도 되는 안전한 이유를 항상 주석으로 남긴다.