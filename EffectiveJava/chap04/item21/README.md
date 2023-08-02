## 21. 인터페이스는 구현하는 쪽을 생각해 설계하라.

: 기존 인터페이스에 메소드를 추가할 수는 있지만, 매끄럽게 동작하리라는 보장은 없다.

### 💎 모든 상황에서 불변식을 해치지 않는 디폴트메소드를 작성하는 건 어렵다.

- 예시 - `removeIf`
- 인터페이스의 디폴트 메소드를 재정의하고, 다른 메소드에서 디폴트 메소드를 호출하기 전에 필요한 작업을 수행하게끔 한다.
- 디폴트 메소드는 기존 구현체에 런타임 오류를 일으킬 수 있다.
- 기존 인터페이스에 디폴트 메소드로 새 메소드를 추가하는 일은 꼭 필요한 경우가 아니면 피한다.
- 디폴트 메소드는 메서드를 제거하거나 기존 메소드의 시그니처를 수정하는 용도가 아니다.
- 디폴트 메소드가 있어도 인터페이스 설계시에는 항상 주의를 기울여야 한다.
- 항상 릴리즈 전에 인터페이스를 구현해 시험해본다.