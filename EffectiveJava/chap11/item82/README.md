## 83. 지연 초기화는 신중히 사용하라.

### 🧭 지연 초기화

: 필드의 값이 처음 필요할때까지 초기화 시점을 미루는 기법이다.

- `정적 필드`와 `인스턴스 필드`에 모두 사용할 수 있다.
- 주로 최적화 용도에 쓰이지만, 클래스와 인스턴스 초기화 때 발생하는 순환문제를 해결하는 효과도 있다.

→ 필요할 때까지 사용하지 말라.

### 🧭 지연 초기화가 필요한 경우

: 그 필드를 사용하는 인스턴스의 비율이 낮은 반면, 그 필드를 초기화하는 비용이 크다면 지연 초기화가 좋다.

- 하지만 실제 지연 초기화를 적용해봐야지만 성능측정이 가능하다.

### 🧭 대부분의 상황에서는 일반적인 초기화가 지연 초기화보다 낫다.

- 인스턴스를 초기화하는 일반적인 방법

```java
class Example {
    // final 한정자를 통한 인스턴스 필드 생성
    private final FieldType field = computeFieldValue();
}
```

- `synchronized` 접근자 방식

```java
class Example {
    private final FieldType field;

    private synchronized FieldType getField() { // 졉근자
        if (field == null) {
            field = computeFieldValue();
        }
        return field;
    }
}
```

→ 성능 때문에 `정적 필드`를 지연 초기화해야한다면 지연 초기화 홀더 클래스 관용구를 사용하자.

- 정적 필드용 지연 초기화 홀더 클래스

```java
class Example {
    private static class FieldHolder {
        static final FieldType field = computeFieldValue();
    }

    private static FieldType getField() {
        return FieldHolder.field;
    }
}
```

: `getField` 가 호출되는 순간 `FieldHolder.field` 가 읽히고 `FieldHolder` 클래스의 초기화를 촉발한다.

→ 성능때문에 `인스턴스 필드`를 초기화해야한다면 이중검사 관용구를 사용하라

```java
class Example {
    private volatile FieldType field;

    private FieldType getField() {
        FieldType result = field; // 첫 번째 검사(락 사용 안함) 초기화 시 한 번만 읽도록 하기 위함
        if (result != null) {
            return result;
        }

        synchronized (this) {
            if (field == null) { // 두 번째 검사 (락 사용)
                field = computeFieldValue();
            }
            return field;
        }
    }
}
```

: 지역 변수 `result`는 필드가 이미 초기화된 상태에서 딱 한번만 읽도록 보장하는 역할 → 빠르게 동작하게 한다.

### 🧭 단일 검사

이중 검사 시, 반복해서 초기화해도 상관없는 인스턴스 필드를 지연 초기화 해야 하는 경우 이중검사에서 두번째 검사를 생략한다.