## 56. 공개된 API 요소에는 항상 문서화 주석을 작성하라.

### 🌳 JavaDoc

: 자바에서 자바독은 **소스코드 파일**에서 **문서화 주석**이라는 특수한 형태로 기술된 설명을 추려 API 문서로 변환해준다.

- JAVA5 → `@literal`, `@code`
- JAVA6 → `@implSpec`
- JAVA9 → `@index`

### 🌳 공개된 모든 클래스, 인터페이스, 메소드 ,필드 선언에 문서화 주석을 달아야 한다.

- 메소드용 문서화 주석에는 해당 메소드와 클라이언트 사이의 규약을 명료하게 기술해야한다.

- how가 아닌, what을 기술한다.

- 호출하기 위한 `전제조건`

- 수행된 후 만족해야하는 `사후조건`

- 부작용(시스템의 상태에 어떠한 변화를 가져오는 지)도 문서화 한다.

- `@param` 태그를 통해 매개변수를 기술할 수도 있다.

- 반환타입이 있다면 `@return` 태그를 통해 기술한다.

- `@param` , `@return` , `@throw` 에는 마침표를 붙이지 않는다.

- 예시

    ```java
     /**
     * Returns a BigInteger whose value is {@code (this * val)}.
     *
     * @implNote An implementation may offer better algorithmic
     * performance when {@code val == this}.
     *
     * @param  val value to be multiplied by this BigInteger.
     * @return {@code this * val}
     */
    public BigInteger multiply(BigInteger val) {
        return multiply(val, false);
    }
    ```

- 자바독 유틸리티는 문서화 주석을 HTML로 변환한다.

- `{@code this * val}` 의 `{@code}` 의 효과

    - 코드용 폰트로 렌더링한다.
    - HTML요소나 다른 자바독 태그를 무시한다.

### 🌳 `@implSpec`

- 해당 메소드와 하위 클래스 사이의 계약을 설명한다.

### 🌳 `@literal`

- <, >, & 등 HTML 메타문자를 포함시키려면 특별한 처리를 해줘야 한다.

### 🌳 읽기 쉬어야 하는게 일반 원칙이다.

- 각 문서화 주석의 첫번째 문장은 해당 요소의 요약설명으로 간주된다.
- 요약설명은 반드시 대상의 기능을 고유하게 기술해야한다.
- 한 클래스(인터페이스)안에 요약설명이 똑같은 멤버(혹은 생성자)가 둘 이상이면 안된다.
- 마침표를 주의해서 사용한다.
- 요약설명이 끝나는 판단기준은 처음 발견되는 마침표로 보기 때문이다.
- 메소드와 생성자의 요약설명은 해당 메소드와 생성자의 동작을 설명하는 동사구여야 한다.

```java
Constructs an empty list with the specified initial capacity.
```

### 🌳 `@index`

- 색인기능이 추가되었다.

```java
This method complies with the {@index IEEE 754} standard.
```

### 🌳 제네릭, 열거타입, 에너테이션, 패키지, 스레드 안전성

- 제네릭

    - 제네릭 타입이나 제네릭 메소드를 문서화할떄는 모든 타입의 매개변수에 주석을 달아야 한다.

    ```java
    /**
     * 키와 값을 매핑하는 객체, 맵은 키를 중복해서 가질 수 없다
     * 즉, 키 하나가 가리킬 수 있는 값은 최대 1개다.
     * 
     * @param <K> 이 맵이 관리하는 키의 타입
     * @param <V> 매핑된 값의 타입
     */
    public interface Map<K, V> { ... }
    ```

- 열거타입

    - 열거타입을 문서화할때는 상수들에도 주석을 달아야 한다.

    ```java
    // 열거 상수 문서화
    /**
     * An instrument section of a symphony orchestra.
     */
    public enum OrchestraSection {
        /** Woodwinds, such as flute, clarinet, and oboe. */
        WOODWIND,
    
        /** Brass instruments, such as french horn and trumpet. */
        BRASS,
    
        /** Percussion instruments, such as timpani and cymbals. */
        PERCUSSION,
    
        /** Stringed instruments, such as violin and cello. */
        STRING;
    }
    ```

- 에너테이션

    - 에너테이션 타입을 문서화할때는 멤버들에도 모두 주석을 달아야 한다.
    - 필드 설명은 명사구로 하고, 애너테이션 타입의 요약 설명은 프로그램 요소에 이 애너테이션을 단다는 것이 어떤 의미인지를 설명한느 동사구로 한다.

    ```java
    // 애너테이션 타입 문서화
    /**
     * Indicates that the annotated method is a test method that
     * must throw the designated exception to pass.
     */
    @Retention(RetentionPolicy.RUNTIME)
    @Target(ElementType.METHOD)
    public @interface ExceptionTest {
        /**
         * The exception that the annotated test method must throw
         * in order to pass. (The test is permitted to throw any
         * subtype of the type described by this class object.)
         */
        Class<? extends Throwable> value();
    }
    ```

- 패키지

    - 패키지를 설명하는 문서화 주석은 `package-info.java` 파일에 작성한다.
    - 모듈 시스템을 사용한다면 모듈 관련 설명은 `module-info.java` 파일에 작성하면 된다.
- 스레드 안전성 & 직렬화 가능성

    - 클래스 혹은 정적 메서드가 스레드 안전하든 그렇지 않든, 스레드 안전 수준을 반드시 API 설명에 포함해야 한다.

### 🌳 **`@inheritDoc`**

: 상위타입의 문서화 주석 일부를 상속할 수 있다.

- 설명문서가 있다면 관련 링크를 제공해주면 좋다.
- 잘 쓰였는지 확인하려면 자바독 유틸리티가 생성한 웹페이지를 읽어봐야 한다.

### 🌳 정리

표준 규약은 일관되게 지키고 문서화 주석에서 HTML 메타문자는 특별하게 취급한다.