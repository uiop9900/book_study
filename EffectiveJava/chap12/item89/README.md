## 89. 인스턴스 수를 통제해야 한다면 readResolve보다는 열거타입을 사용하라.

### 🧨 싱글톤을 직렬화 하면?

- 싱글톤은 하나의 인스턴스만 생성하는 것을 말하는데, 이 싱글톤에 `Implements Serializable`을 하면 더이상 싱글톤이 아니게 된다.
- 해당 클래스가 초기화될 때 만들어진 인스턴스와는 다른 별개의 인스턴스를 반환한다.

### 🧨 readResolve

: `readResolve`를 사용하면 `readObject`가 만들어낸 인스턴스를 다른 것으로 대체할 수 있다.

- 역직렬화한 객체의 클래스가 `readResolve` 를 제대로 구현했다면!
- 역직렬화 후 새로 생성된 객체 → `readResolve` 호출 → `readResolve`가 반환한 객체가 새로 생성된 객체 대신 반환된다. → 새로 생성된 객체는 가비지 컬렉션 대상.

### 🧨 **Transient**

: 객체의 필드 중에 역직렬화 하지 않을 것들을 지정할때 사용한다.

- `readResolve`를 인스턴스 통제 목적으로 사용한다면 객체 참조 타입 인스턴스 필드는 모두 `transient`로 선언하자.
- 이렇게 하지 않으면 역직렬화 과정에서 역직렬화 인스턴스를 가지고 올 수 있다. → 싱글톤 깨진다.

### 🧨 Enum을 사용한다.

- `enum`을 사용하면 자바가 선언한 상수 외에 다른 객체가 없음을 보장해주기 때문에 사용한다.
- 열거타입으로 표현이 불가능할 때  `readResolve` 메서드를 사용한다.
- final 클래스라면 readResolve() 메서드는 private이어야 한다.
  - protected나 public으로 선언한다면 재정의하지 않은 모든 하위클래스에서 사용할 수 있는데, 이 때 하위 클래스의 인스턴스를 역직렬화하면 상위 클래스의 인스턴스를 생성하여 `ClassCastException`이 발생할 수 있다.