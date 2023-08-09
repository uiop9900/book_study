## 39. 명명 패턴보다는 애너테이션을 사용하라.

### 🕹️ 명명패턴과 단점

```java
public class HelloTest extends TestCase {
	public void testGetUsers() {
		return getUsers();
	}
}
```

- 오타가 나면 안된다.
- 올바른 프로그램 요소에서만 사용되리라 보증할 방법이 없다.
- 프로그램 요소를 매개변수로 전달할 마땅한 방법이 없다.

### 🕹️ 메타 애너테이션

: 애너테이션 선언에 다는 애너테이션

```java
/**
 * 테스트 메서드임을 선언하는 애너테이션이다.
 * 매개변수 없는 정적 메서드 전용이다.
 */
@Retention(RetentionPolicy.RUNTIME) // @Test가 런타임에도 유지되어야 한다는 표시이다. 
@Target(ElementType.METHOD) // @Test가 반드시 메서드 선언에서만 사용돼야 한다. 
public @interface Test { // @Test
}
```

### 🕹️ 마커 애너테이션

: 아무 매개변수 없이 단순히 대상에 마킹하는 애너테이션

```java
public class RunTests {
    public static void main(String[] args) throws Exception {
        int tests = 0;
        int passed = 0;
        Class<?> testClass = Class.forName(args[0]);
        for (Method m : testClass.getDeclaredMethods()) {
            if (m.isAnnotationPresent(Test.class)) { // test annontaion을 단 메소드를 호출한다.
                tests++;
                try {
                    m.invoke(null);
                    passed++;
                } catch (InvocationTargetException wrappedExc) {
                    Throwable exc = wrappedExc.getCause();
                    System.out.println(m + " 실패: " + exc);
                } catch (Exception exc) {
                    System.out.println("잘못 사용한 @Test: " + m);
                }
            }
        }
        System.out.printf("성공: %d, 실패: %d%n",
                passed, tests - passed);
    }
}
```

### 🕹️ 매개변수를 하나 받는 애너테이션 타입

```java
/**
 * 명시한 예외를 던져야만 성공하는 테스트 메서드용 애너테이션
 */
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface ExceptionTest {
    Class<? extends Throwable> value(); // Throwable을 확장한 클래스의 Class를 매개변수로 받는다.
}
```

```java
...위와 동일
            if (m.isAnnotationPresent(ExceptionTest.class)) {
                tests++;
                try {
                    m.invoke(null);
                    System.out.printf("테스트 %s 실패: 예외를 던지지 않음%n", m);
                } catch (InvocationTargetException wrappedEx) {
                    Throwable exc = wrappedEx.getCause();
                    Class<? extends Throwable> excType =
														// 해당 어노테이션을 단 메소드에서 value(매개변수)를 가져와서 올바른 예외를 던졌는지 확인한다.
                            m.getAnnotation(ExceptionTest.class).value();
                    if (excType.isInstance(exc)) {
                        passed++;
                    } else {
                        System.out.printf(
                                "테스트 %s 실패: 기대한 예외 %s, 발생한 예외 %s%n",
                                m, excType.getName(), exc);
                    }
                } catch (Exception exc) {
                    System.out.println("잘못 사용한 @ExceptionTest: " + m);
                }
            }
        }

        System.out.printf("성공: %d, 실패: %d%n",
                passed, tests - passed);
    }
}
```

### 🕹️ 배열을 매개변수로 받는 경우

1. 매개변수를 배열로 넣는다.

  1. () 안에 `{}`넣고 쉼표로 구분해서 여러 개의 매개변수를 받는다.

    ```java
    @ExceptionTest({IndexOutOfBoundsException.class, NullPointerException.class})
    public static void doublyBad() {   // 성공해야 한다.
    }
    ```

2. `@Repeatable` 을 사용한다.

  1. `@Repeatable` 을 반환하는 컨테이너 애너테이션을 추가한다.
  2. Class 객체를 매개변수로 전달한다.
  3. 컨테이너 애너테이션에 `@Retention` 과 `@Target` 을 설정한다.

    ```java
    @Retention(RetentionPolicy.RUNTIME)
    @Target(ElementType.METHOD)
    @Repeatable(ExceptionTestContainer.class)
    public @interface ExceptionTest {
        Class<? extends Throwable> value();
    }
    
    // a. 컨테이너 애너테이션
    @Retention(RetentionPolicy.RUNTIME) // c.
    @Target(ElementType.METHOD) // c.
    public @interface ExceptionTestContainer {
    		ExceptionTest[] value();
    }
    ```

    ```java
    // b. 반복해서 사용한다.
    @ExceptionTest(IndexOutOfBoundsException.class)
    @ExceptionTest(NullPointerException.class)
    public static void doublyBad() {   
    }
    ```


### 🕹️ 정리

- 애너테이션으로 할 수 잇는 일을 명명패턴으로 처리할 이유가 없다.
- 자바 프로그래머라면 예외 없이 자바가 제공하는 애너테이션 타입들을 사용해야 한다.