## 15. 클래스와 멤버의 접근 권한을 최소화한다.

: 잘 설계되었는지 알 수 있는건 `데이터`와 `내부 구현 정보`를 `외부 컴포넌트`로부터 얼마나 잘 숨겼는지에 따라 알 수 있다.

### 💎 정보은닉(캡슐화)의 장점

- 컴포넌트를 병렬로 개발이 가능하다.
- 각 컴포넌트를 빠르게 파악할 수 있어서 시스템 관리 비용을 낮춘다.
- 다른 컴포넌트에 영향없이 해당 컴포넌트만 최적화 할 수 있기 때문에 성능 최적화에 도움을 준다.
- 소프트웨어의 재사용성을 높인다.
- 개별 컴포넌트의 동작검증이 가능하다.

### 💎 모든 클래스와 멤버의 접근성을 가능한한 좁혀야 한다.

- 가장 바깥의 클래스, 인터페이스: `public`, `package-private`

    - 패키지 외부에서 쓸 이유가 없다면 `package-private` 선언한다.
- 한 클래스에서만 사용하는 package-private 클래스, 인터페이스 : `private static`으로 중첩

    - 그러면 바깥의 하나의 클래스만 접근이 가능하다.

  → 굳이 public일 필요가 없는 클래스는 `package-private`으로 변경하는 것이다.

  :package-private는 내부구현에 속하기 때문이다.

- 멤버(필드, 메서드, 중첩클래스, 중첩 인터페이스) 에 선언할 수 있는 접근수준

    ```
    private :선언한 클래스 안에서만 접근가능
    package-private: 소속된 패키지 안의 모든 클래스에서 접근 가능
    protected:  package-private를 포함하고 하위 클래스에서도 접근가능
    public: 모든 곳에서 접근가능
    ```

- 단, Serializable을 구한 클래스는 공개 api가 될 수 있다.


package-private → protected: 접근 가능한 대상의 범위가 확 늘어난다. protected는 적을수록 좋다.

### 💎 하지만 상위 클래스의 메소드를 정의할 때는 접근수준이 상위 클래스보다 좁게가 불가하다.

: 상위클래스의 인스턴스는 하위클래스의 인스턴스로 대체사용이 가능해야 한다.(리스코프의 치환원칙)

→ 안 지키면 컴파일 시 오류 → Public으로 선언

- 테스트 코드는 대상 패키지와 동일한 곳에 두면 package-private요소에 접근가능이다.

### 💎 public 클래스의 필드는 되도록 public이어서는 안된다.

: public 가변 필드를 갖게 되면 일반적으로 스레드 안전하지 않다.

- 상수라면 public static final 로 공개해도 된다.

```java
public static final String USER_NAME = "JIA";
```

- 길이가 0이 아닌 배열은 변경이 가능하니 상수로 선언하지 않는다.

```java
// 보안 허점이 숨어 있다. 
    public static final Thing[] VALUES = { ... };
```

- 해결책 1) 앞 코드의 `public` 배열을 `private`으로 만들고 `public 불변 리스트`를 추가한다.

```java
private static final Thing[] PRIVATE_VALUES = { ... };
    public static final List<Thing> VALUES = 
        Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES));
```

- 해결책 2) 배열을 `private`으로 만들고 그 복사본을 `반환하는 public 메서드`를 추가한다. (방어적 복사)

```java
private static final Thing[] PRIVATE_VALUES = { ... };
    public static final Thing[] values)_ {
        return PRIVATE_VALUES.clone();
    }
```

### 💎 모듈의 접근수단

: 패키지는 클래스의 묶음이고 모듈은 패키지들의 묶음이다.

- JAR 파일을 자신의 모듈 경로가 아닌 애플리케이션의 `클래스패스(classpath)`에 두면 그 모듈 안의 모든 패키지는 마치 모듈이 없는 것처럼 행동한다.
- 즉, 모듈이 공개 했는지 여부와 상관없이, `public 클래스가 선언한 모든 public 혹은 protected 멤버를 모듈 밖에서도 접근할 수 있게 된다.`
- 새로 등장한 이 접근 수준을 적극 활용한 대표적인 예가 바로 `JDK` 자체다.
- 자바 라이브러리에서 공개하지 않은 패키지들은 해당 모듈 밖에서는 절대로 접근할 수 없다.