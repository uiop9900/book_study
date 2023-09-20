## 87. 커스텀 직렬화 형태를 고려해보라.

### 🧨 고심해보고 괜찮다고 판단될때만 기본 직렬화 형태를 사용하라.

- 클래스가 `Serializable` 를 구현하고 기본 직렬화 형태를 사용하면, 다음 릴리스에서 버리려 한 현재 구현을 쉽게 버리지 못한다.
- 어떤 객체의 기본 직렬화 형태는 그 객체를 나름 효율적으로 인코딩한다.
- 객체의 물리적 표현과 논리적 내용이 같다면 기본 직렬화 형태라도 무방하다.

```java
public class Name implements Serializable {

  /**
   * 성. null이 아니어야함
   * @serial
   */
  private final String lastName;

  /**
   * 이름. null이 아니어야 함.
   * @serial
   */
  private final String firstName;

  /**
   * 중간이름. 중간이름이 없다면 null.
   * @serial
   */
  private final String middleName;
}
```

: 논리적으로 3개의 문자열로 구성되어있고, 인스턴스 필드들이 이를 논리적 구성요소에 정확히 반영했다.

- 기본 직렬화 형태가 적합하다고 판단해도, 불변식 보장과 보안을 위해 `readObject` 메소드를 제공해야할 때가 많다.

### 🧨 ****기본 직렬화 형태에 적합하지 않은 클래스****

```java
public final class StringList implements Serializable {
  private int size = 0;
  private Entry head = null;

  private static class Entry implements Serializable {
    String data;
    Entry next;
    Entry previous;
  }
}
```

: 물리적 표현과 논리적 표현의 차이가 클 때의 문제점

1. 공개 API가 현재 내부 표현 방식에 영원히 묶인다.
  1. 다음 릴리스에서 내부 표현 방식을 바꾸더라도 `StringList` 클래스는 여전히 연결 리스트로 표현된 입력도 처리할 수 있어야 한다. → 관련 코드를 제거할 수 없다.
2. 너무 많은 공간을 차지할 수 있다.
  1. 직렬화 형태가 너무 커져서 디스크에 저장하거나 네트워크로 전송하는 속도가 느려진다.
3. 시간이 너무 많이 걸린다.
  1. 직렬화 로직은 객체 그래프의 위상에 관한 정보가 없으니 그래프를 직접 순회해볼 수밖에 없다.
4. 스택 오버플로를 일으킬 수 있다.
  1. 기본 직렬화 과정은 객체 그래프를 재귀 순회하는데 중간정도 크기의 객체 그래프에서도 스택 오버플로 에러가 날 수 있다.

### 🧨 그렇다면 합법적인 직렬화 형태는?

```java
public final class StringList implements Serializable {
    private transient int size = 0;
    private transient Entry head = null;

    // 이제는 직렬화되지 않는다.
    private static class Entry {
        String data;
        Entry next;
        Entry previous;
    }

    // 지정한 문자열을 이 리스트에 추가한다.
    public final void add(String s) {...}

    /**
     * 이 {@code StringList} 인스턴스를 직렬화한다.
     *
     * @serialData 이 리스트의 크기(포함된 문자열의 개수)를 기록한 후
     * ({@code int}), 이어서 모든 원소를(각각은 {@code String})
     * 순서대로 기록한다.
     */
    private void writeObject(ObjectOutputStream s) throws IOException {
     	//기본 직렬화를 수행한다.
        s.defaultWriteObject();
        s.writeInt(size);

        // 커스텀 역직렬화를 수행한다.
        // 모든 원소를 올바른 순서로 기록한다.
        for (Entry e = head; e != null; e = e.next)
            s.writeObject(e.data);
    }

    private void readObject(ObjectInputStream s) throws IOException, ClassNotFoundException {
        //기본 역직렬화를 수행한다.
        s.defaultReadObject();
        int numElements = s.readInt();

        // 커스텀 역직렬화 부분
        // 모든 원소를 읽어 이 리스트에 삽입한다.
        for(int i = 0; i < numElements; i++) {
            add((String) s.readObject());
        }
    }
}
```

- 필드가 다 `transient` 면 `defaultWriteObject`, `defaultReadObject`를 호출하지 않아도 된다고 생각하지만, 둘 다 호출해야한다.
- 이렇게 해야 `transient` 가 아닌 필드가 추가되어도 상호(상위-하위)호환이 되기 때문이다.
- `transient` 를 사용하면 역직렬화시에 기본값으로 초기화된다.
  - null, 0, false

### 🧨 객체의 불변식이 깨지는 경우, 직렬화를 주의한다.

- 해시 테이블
  - key-value 엔트리들을 담은 해시 버킷
  - 키에서 구한 해시코드에 따라 어떤 버킷에 담을지 달라진다.
  - 계산 방식은 구현에 다라 달라진다.
  - 해시테이블을 직렬화한 후 역직렬화하면 불변식이 심각하게 훼손된 객체들이 생겨날 수 있다.

### 🧨 동기화 메커니즘을 직렬화에도 적용해야 한다.

: 객체의 전체 상태를 읽는 메소드에 `synchronized` 를 적용해서 직렬화를 한다.

```java
private synchronized void writeObject(ObjectOutputStream s) throws IOException {
        s.defaultWriteObject();
        }
```

### 🧨 직렬화 가능 클래스 모두에 직렬 버전 UID를 명시적으로 부여하자.

```java
private static final long serialVersionUID = -232923283928929; // 무작위의 Long 값
```

- 이렇게 하면 직렬 버전 UID가 일으키는 잠재적인 호환성 문제가 사라진다.
- 성능도 조금 빨라지는데 직렬 버전 UID를 명시하지 않으면 런타임에 이 값을 생성하느라 복잡한 연산을 수행하기 때문
- 기존 클래스와의 호환성을 끊고 싶으면 위의 값을 변경하면 된다.
- 구버전으로 직렬화된 인스턴스들과 호환성을 끊으려는 경우를 제외하고는, 직렬 버전 UID를 절대 수정하지 않는다.