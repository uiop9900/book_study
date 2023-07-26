## 22. 인터페이스는 타입을 정의하는 용도로만 사용하라.

- 인터페이스는 자신을 구현한 클래스의 인스턴스를 참조할 수 있는 타입역할이다.
- 클래스가 어떤 인터페이스를 구현한다. == 자신의 인스턴스로 무엇을 할 수 있는지 클라이언트에게 얘기한다.

### 💎 인터페이스를 잘못 사용한 예시

- 상수 인터페이스
  - `static final` 필드로만 가득한 인터페이스
  - 클래스 내부에서 사용하는 상수는 `내부구현`이다.
  - 상수인터페이스로 만들었다는 것은 내부구현을 클래스 API로 노출하는 것이다.

    ```java
    public interface Numbers {
    	static final double TODAY_NUMBER = 2234.1234;
    	static final double YESTERDAY_NUMBER = 543.643134;
    	static final double TOMORROW_NUMBER = 87.5675673;
    }
    ```


### 💎 상수를 공개하고 싶다면?

- 특정 클래스나 인터페이스와 강하게 연관된 상수라면
  - 해당 클래스, 인터페이스에 추가한다.
- 열거타입(enum)
- 유틸리티 클래스
  - class로 만들고 생성자를 막는다.

    ```java
    public class Numbers {
    	private Numbers() {}
    
    	static final double TODAY_NUMBER = 2234.1234;
    	static final double YESTERDAY_NUMBER = 543.643134;
    	static final double TOMORROW_NUMBER = 87.5675673;
    }
    ```


