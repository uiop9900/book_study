## 19. 상속을 고려해 설계하고 문서화하라. 그러지않았다면 상속을 금지하라.

### 💎 상속을 고려한 설계와 문서화란?

- 상속용 클래스는 재정의할 수 있는 메서드들을 내부적으로 어떻게 이용하는지(자기사용) 문서로 남겨야한다.
- 재정의가 가능한 메소드들이 호출될 수 있는 모든 상황을 문서로 남긴다.
  - `@implSpec` 을 사용한다. - 주석안에서 사용하는 어노테이션
  - `Implementation Requirements ~` : 내부 동작 방식을 설명하는 곳

    ```java
    /**
    	 * @implSpec 정기결제 등록
    */
    ```

- 좋은 API문서는 `무엇`을 하는지에 초점이지만, 해당 건을 내부구현방식(`어떻게`)을 설명해야한다.
- 클래스의 내부 동작 과정 중간에 끼어들 수 있는 훅을 잘 선별해서 protected 메소드 형태로 공개해야 할 수 있다.

    ```java
    protected void removeRange(int fromIndex, int toIndex)
    
    ...
     이 리스트는 혹은 이 리스트의 부분리스트에 정의된 clear 연산이 이 메서드를 호출한다.
    ```

  - 해당 클래스는 removeRange보다는 그 하위의 clear 연산에 관심이 있다.
  - 해당 메소드는 clear메소드의 성능을 위해 만들어진 메소드이다. → `protected`로 선언

### 💎 상위클래스를 시험하는 방법은 직접 하위클래스를 만들어보는 것이 유일하다.

: 상속용으로 설계한 클래스는 배포 전에 반드시 하위클래스를 만들어서 검증한다.

- 상속용 클래스의 생성자는 직,간접적으로 재정의 가능 메소드를 호출해서는 안된다.
  - 상위클래스의 생성자가 하위클래스의 생성자보다 먼저 호출된다.
  - 상위클래스에서 하위클래스의 재정의 메소드를 호출하면?
    - 상위클래스 생성자 > 하위클래스 재정의 메소드 > 하위클래스 생성자
    - 프로그램 오작동🧨
- `clone`, `readObject` 메소드는 직간접적으로 재정의 가능 메소드를 호출해서는 안된다.
  - `clone`, `readObject` 메소드는 생성자와 비슷한 효과를 낸다. → 상단과 동일한 이야기.
  - 특히 clone은 원본 객체에도 피해를 줄 수 있다.
- `Serializable`을 구현한 상속용 클래스가 `readResolver` 나 `writeReplace`를 갖는다면, 해당 메소드들은 protected로 선언한다.
  - `private`로 선언한다면 하위클래스에서 무시당하기 때문이다.

→ 클래스를 상속용으로 선언하려면 많은 노력과 해당 클래스에 제약이 생긴다.

### 💎 상속용으로 설계하지 않은 클래스는 상속을 금지한다.

### 일반적인 구체클래스란?

new를 이용해서 생성할 수 있는 클래스를 말한다.

- 상속을 금지하는 방법
  - `final`로 선언한다.
  - 모든 생성자를 `private`, `package-private`으로 선언하고 public 정적 팩토리를 만든다.
- 하지만 상속을 하지 못하면 불편함이 있다.
  - 핵심기능의 interface, 해당 Interface를 구현한 클래스 → 문제 없음
  - 일반적으로는 구체 클래스를 임의로 상속해 기능들을 추가했다.
- 클래스 내부의 재정의 가능 메소드를 사용하지 않게 만들고 그 사실을 문서로 만든다.

### 💎 클래스 동작을 유지하면서 재정의 가능 메소드를 제거하는 방법

: 재정의 가능 메소드를 `private` 도우미 메소드로 만들고 다른 곳에서 호출시 해당 도우미 메소드를 부르게끔한다.

- 기존

```java
public class Concrete {

    public Concrete() {}

    public void overrideMe() { // 다른곳에서도 호출가능
        System.out.println("overrideMe");
    }

    public void doSomething() {
        overrideMe();
    }
}
```

- private 도우미메소드
  - 호출만 가능하고 재정의는 불가하다.

```java
public class BetterConcrete {

    public BetterConcrete() {}

		// 도우미메소드를 호출
    public void overrideMe() {
        helper();
    }

    private void helper() { // private 도우미 메소드로 변경
        System.out.println("overrideMe");
    }

		// 도우미메소드를 호출
    public void doSomething() {
        helper();
    }
}
```

