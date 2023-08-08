## 35. ordinal 메소드 대신 인스턴스 필드를 사용하라.

### 🕹️ ordinal 메소드

- 해당 상수가 그 열거 타입에서 몇번째 위치인지 반환하는 메소드

    ```java
    public enum Ensemble {
        SOLO, DUET, TRIO, QUARTET, QUINTET, SEXTET, SEPTET, OCTET, NONET, DECTET;
    
        public int numberOfMusicians() {
            return ordinal() + 1;
        }
    }
    ```

  - 동작하지만, 순서가 바뀌게 되면 오동작한다.
  - 또한 중간값을 비워둘 수 없다. 무조건 순차적으로 값을 넣어야 한다.

### 🕹️ 열거 상수 타입에 연결된 값은 ordinal 메소드가 아닌, 인스턴스 필드에 저장하자.

```java
public enum Ensemble {
    SOLO(1),
    DUET(2),
    TRIO(3),
    QUARTET(4),
    QUINTET(5),
    SEXTET(6),
    SEPTET(7),
    OCTET(8),
    DOUBLE_QUARTET(8),
    NONET(9),
    DECTET(10),
    TRIPLE_QUARTET(12);

    private final int numberOfMusicians;

    Ensemble(int numberOfMusicians) {
        this.numberOfMusicians = numberOfMusicians;
    }

    public int numberOfMusicians() {
        return numberOfMusicians;
    }
}
```

- 중간 숫자도 비워놓을 수 있고 상수에 따라 값이 변동될 여지가 없다.

→ ordinal 메소드는 `EnumSet`, `EnumMap`과 같이 열거 타입 기반의 번용 자료구조에 쓸 목적으로 설계되었다.