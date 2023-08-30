## 62. 다른 타입이 적절하다면 문자열 사용을 피하라.

: 문자열을 쓰지말아야 하는 사례에 대해 알아보자.

### 🎃 문자열은 다른 값 타입을 대신하기에 적합하지 않다.

- 수치형 → int, float, BigInteger
- 예/아니요 → enum, boolean

### 🎃 문자열은 열거타입을 대신하기에 적합하지 않다.

### 🎃 문자열은 혼합타입을 대신하기에 적합하지 않다.

### 🎃 문자열은 권한을 대신하기에 적합하지 않다.

예시) 각 스레드가 자신만의 변수를 갖는다.

이 때, 클라이언트가 고유한 키를 제공해야 한다. → 같은 키가 된다면 의도하지 않게 변수를 공유하게 된다.

- 문자열로 권한 부여

```java
public class ThreadLocal {
    private ThreadLocal() {
    }

    // 현 스레드의 값을 키로 구분해 저장한다.
    public static void set(String key, Object value);

    // (키가 가리키는) 현 스레드의 값을 반환한다.
    public static Object get(String key);
}
```

- Key 클래스로 권한 부여

```java
public class ThreadLocal {
    private ThreadLocal() {
    }

    public static class Key { // (권한)
        Key() {
        }
    }

    public staatic getKey() { 
        return Key;
    }

    public static void set(Key key, Object value); // 정적메소드일 필요가 없다.

    public static Object get(Key key); // 정적메소드일 필요가 없다.
}
```

- Key 자체를 ThreadLocal로 변경한다.
  - `Obejct`는 실제 타입으로 형변환 해야해서 `T`로 변경한다.

```java
// Key -> ThreadLocal
public final class ThreadLocal { // 
    public ThreadLocal() {
    }
    public void set(Object value);
    public Object get();
}
```

- 완성본
```java
public final class ThreadLocal<T> {
  public ThreadLocal();
  public void set(T value);
  public T get();
}

```