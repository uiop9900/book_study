## 18. 상속보다는 컴포지션을 사용하라.

패키지를 넘어, 다른 패키지의 구체 클래스를 상속하는 것은 위험하다.

### 💎 상속은 캡슐화를 깨뜨린다.

- 상위 클래스가 어떻게 구현되는 지에 따라 하위클래스가 영향을 미칠 수 있다.
  - 예시 코드

      ```java
      public class CustomHashSet<E> extends HashSet {
          private int addCount = 0;
      
          public CustomHashSet(){}
      
          public CustomHashSet(int initCap, float loadFactor){
              super(initCap,loadFactor);
          }
      
          @Override
          public boolean add(Object o) {
              addCount++;
              return super.add(o);
          }
      
          @Override
          public boolean addAll(Collection c) {
              addCount+=c.size();
              return super.addAll(c);
          }
      
          public int getAddCount() {
              return addCount;
          }
      
      }
      ```

  - 자기사용 여부는 해당 클래스의 내부 구현 방식이며, 이런 자기사용은 다음 릴리스에서도 유지될 지 알 수 없다.
    - 자기사용(self-use): 자기 자신의 다른 부분을 사용
- 다음 릴리스에서 상위클래스에 새로운 클래스를 추가한다.
  - 모든 메소드를 재정의해 validation 체크를 한다.
  - 하위클래스에서 재정의하지 못한 새로운 메소드를 사용해 원소를 추가하면 하위클래스들은 난리가 난다.

**→** **메서드 재정의가 원인이다.**

### 💎 클래스 확장시에 메소드를 재정의하는 대신 새로운 메소드를 추가한다?

- 하위클래스에서 메소드 추가했는데, 상위 클래스도 해당 메소드와 동일하지만 반환값만 다른 메소드가 추가되면 컴파일 자체가 되지 않는다.
- 하위클래스에서 추가된 메소드는 상위클래스에서 정의할 예정인 메소드와 완벽하게 동일할 확률은 희박하다.

### 💎 컴포지션(Composition) 한다.

: 기존 클래스를 확장하는 대신, 새로운 클래스를 만들고 private 필드로 기존 클래스의 인스턴스를 참조하게 한다.

- 전달(forwarding)
  - 새로운 클래스에 컴포지션된 메소드들은 기존 클래스의 메소드를 호출해 반환한다.
- 전달 메소드 (forwarding method)
  - 새 클래스의 메소드들

→ 새로운 클래스는 기존 클래스로부터 영향받지 않는다.

```java
class Engine {} // The Engine class.

class Automobile {} // Automobile class which is parent to Car class.

class Car extends Automobile { // Car is an Automobile, so Car class extends Automobile class.
  private Engine engine; // Car has an Engine so, Car class has an instance of Engine class as its member.
}
```

### 💎 데코레이터 패턴(예시)

- Set을 구현한 클래스

    ```java
    public class ForwardingSet<E> implements Set<E> {
        private final Set<E> s;
    
        public ForwardingSet(Set<E> s) {
            this.s = s;
        }
    
        public int size() {
            return 0;
        }
    // 계측기능(생략)
    ```

  - `Set`을 구현해 그 안에 계측기능을 덧씌워서 새로운 `Set`을 구현했다.
  - 생성자를 제공한다.
  - 이렇게 구현하면 어떠한 `Set`이 와도 `ForwardingSet`을 통해 계측이 가능하다.
- 래퍼클래스
  - 다른 Set인스턴스(`ForwardingSet`)을 감싸고 있어서 래퍼클래스로 칭한다.

    ```java
    public class InstrumentedSet<E> extends ForwardingSet<E> {
        private int addCount = 0;
    
        public InstrumentedHashSetUseComposition(Set<E> s) {
            super(s);
        }
    // 생략
    ```


### 💎 래퍼클래스의 단점

- 콜백 프레임워크와 어울리지 않는다.
  - 자기 자신의 참조를 다른 객체에 넘겨서 다음 콜백때 사용하게끔한다.
  - 나(class)는 나의 래퍼클래스의 유무를 모르니까 나(this) 자체를 반환하게되고, 그러면 콜백시에 내부 객체를 호출된다.
  - == SELF 문제

### 💎 상속시 확인할 것

- 상속은 is-a 관계일때만 한다.
  - 클래스 B는 정말 A인가?
- 컴포지션을 써야하는 상황에서 상속을 사용하는건 내부구현을 불필요하게 노출하는 것이다.
- 확장하려는 클래스의 api는 아무런 결함이 없는가?
  - 상속은 상위클래스의 API의 결함까지도 그대로 승계한다.