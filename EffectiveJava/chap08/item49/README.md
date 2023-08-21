## 49. 매개변수가 유효한지 검사하라.

매개변수가 특정조건을 만족하기 바란다면 반드시 문서화하고 몸체가 시작하기 전에 잡아야 한다.

→ 오류는 가능한 한 빨리 발생한곳에서 잡아야 한다.

### 🌳 매개변수 검사를 제대로 하지 않으면?

- 메소드가 수행되는 중간에 모호한 예외를 던지며 실패할 수 있다.
- 메소드가 잘 수행되는데 잘못된 값을 반환한다.
- 잘 수행되지만 객체를 이상한 상태로 만들어놔서 추후 언제 에러를 만들어낼 지 모르는 폭탄으로 만든다.

→ public, protected 메소드는 매개변수 값이 잘못됐을 때 던지는 예외를 문서화 해야한다.

→ `@throws` 자바독 태그 이용하기

### 🌳 매개변수 검사

- 전형적인 예시

    ```java
    public class Item49 {
        /**
         * (현재 값 mod m) 값을 반환한다. 이 메서드는
         * 항상 음이 아닌 BigInteger를 반환한다는 점에서 remainder 메서드와 다르다.
         * @param m 계수(양수여야 한다)
         * @return 현재 값 mod m
         * @throws ArithmeticException m이 0보다 작거나 같으면 발생한다.
         */
        public BigInteger mod(BigInteger m) {
            if (m.signum() <= 0) {
                throw new ArithmeticException("계수(m)는 양수여야 합니다. " + m);
            }
            // 계산 수행(생략)
            return null; 
        }
    }
    ```

  - 반환값이 `BigInteger`이라, 해당을 기준으로 매개변수 검사를 했다.
  - `null`일때에 대한 에러가 명시되어 있지 않다.
- `**requireNonNull` 메소드**

    ```java
    this.strategy = Objects.requiredNonNull(strategy, "전략");
    ```

  - 유연하고 사용하기 편해 더이상 null 검사를 수동으로 하지 않아도 된다.
  - 입력 그대로 반환하기때문에 순수한 null 검사 목적으로 사용이 가능하다.
- **`단언문(assert)` 으로 유효성 검증**

    ```java
    private static void sort(long a[], int offset, int length) {
    	assert a != null;
    	assert offset >= 0 && offset <= a.length;
    	assert length >= 0 && length <= a.length = offset;
    	... // 계산 수행
    }
    ```

  - 조건이 무조건 참이라고 선언한다.
  - 실패하면 `AssertionError`를 던진다.
  - 런타임에 아무런 효과도, 성능 저하도 없다. (단, java를 실행할 때 명령줄에서 -ea 혹은 —`enableassertions` 플래그 설정하면 런타임에 영향을 준다)
  - 메소드가 직접 사용하는 매개변수가 아니기 때문에 더 신경써서 검사해야한다.

### 🌳 매개변수 검사를 하지 않는 경우

- 검사비용이 지나치게 높거나 실용적이지 않다.
- 계산과정에서 암묵적으로 검사가 수행될때
  - `sort` 정렬시, 암묵적으로 다 비교가 가능하다.

### 🌳 유효성 검사가 실패한다면?

계산과정에서 유효성검사가 일어날때, 잘못된 예외를 던지기도 한다.

`잘못된 매개변수인 경우의 에러` ≠ `API문서에서 던지기로 한 예외`

→ API 문서에 기재된 예외로 변경한다.