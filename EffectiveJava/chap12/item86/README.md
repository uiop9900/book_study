## 86. Serializable을 구현할지는 신중히 결정하라.

### 🧨 `Serializable` 을 구현하면 릴리스한 뒤에는 수정하기 어렵다.

- 클래스 선언에 `implements Serializable` 만 덧붙이면 인스턴스가 직렬화가 가능하다.
- `Serializable` 를 구현하게 되면, 직렬화된 바이트 스트림 인코딩도 하나의 공개 API가 된다. → 계속 지원해야한다.

→ 직렬화 가능 클래스를 만들고자 한다면, 후일까지 고려해서 잘 짜야한다.

### 🧨 단점 - 예시

1. 자동으로 생성되는 값에 의존하면 호환성이 쉽게 깨진다 → `InvalidClassException` 발생
  1. 스트림 고유식별자 (UID)
    1. 직렬화된 클래스는 고유 식별 번호를 부여받는다.
    2. 해당 값에는 대부분의 클래스 멤버들이 고려된다. → 하나라도 수정되면 값도 변한다.
2. 버그와 보완 구멍이 생길 위험이 높다.
  1. 객체는 생성자를 기본으로 생성한다. → 직렬화는 우회해서 객체를 생성하는 기법이다. 즉, 숨은 생성자
  2. 기본 역직렬화를 사용하면 접근에 쉽게 노출된다.
3. 해당 클래스의 신버전을 릴리스할 때 테스트할 것이 늘어난다.
  1. 신버전이 구버전을 직렬화하고 신버전을 구버전으로 역직렬화 가능한지
  2. 다 테스트 해봐야 한다.

### 🧨 `Serializable` 구현 여부는 가볍게 결정할 사안이 아니다.

- `BigInteger`, `Instant`와 같이 값은 `Serializable` 를 구현하고
- 스레드 풀처럼 동작하는 클래스는 `Serializable` 를 구현하지 않았다.

### 🧨 상속용으로 설계된 클래스는 대부분 `Serializable` 구현하면 안되고, 인터페이스도 대부분 `Serializable` 을 확장해서는 안된다.

- 해당 클래스들을 확장하거나 구현하는데에 커다란 부담을 지우게 된다.

### 🧨 클래스의 인스턴스가 직렬화와 화강이 모두 가능하다면?

- 불변식을 보장해야한다면, 하위 클래스에서 `finalize` 메서드를 재정의 못하게 한다.
  - `finalize` 메소드를 자신이 재정의하면서 `final`로 선언한다.
- 인스턴스필드 중 기본값으로 초기화되면 위배되는 불변식이 있다면 `readObjectNoData` 메소드를 반드시 추가한다.

```java
private void readObjectNoData() throws InvalidObjectException {
    throw new InvalidObjectException("스트림 데이터가 필요합니다.");
}
```

### 🧨 `Serializable` 를 구현하지 않기로 했다면?

- 상속용 클래스인데 직렬화를 지원하지 않으면, 하위클래스는 직렬화 구현하기 힘들다.
- 역직렬화하려면 상위클래스는 매개변수가 없는 생성자를 제공해야 한다. → 하위클래스에서 직렬화 프록시 패턴을 사용한다.

### 🧨 내부 클래스는 직렬화를 구현하지 않는다.

: 내부 클래스는 지역변수 값들을 저장하기 위해 컴파일러가 생성한 필드들이 자동으로 추가된다.

언제 어떻게 추가될 지 모른다. → 내부 클래스에 대한 기본 직렬화 형태가 분명하지 않다.

- 단, 정적 멤버 클래스는 `Serializable` 를 구현해도 된다.