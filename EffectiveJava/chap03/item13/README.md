## 13. clone 재정의는 주의해서 진행하라.

### 💣 Cloneable은 메소드가 없는 인터페이스이다.

: clone메소드는 Object안에서 선언되어 있고, protected라 override해서 사용해야한다.

- 보통의 인터페이스: 인터페이스가 정의한 기능을 구현해서 사용한다.
- Cloneable: 상위클래스인 Object에 선언된 clone 메소드의 동작 방식을 변경한다.
    - clone 호출 시, 복제한 객체들을 반환하거나 `CloneNotSupportedException`을 던진다.

### 💣 Cloneable은 어떻게 구현하나요?

- 가변객체를 clone 하는 경우, 원본의 객체가 수정되면 해당 객체를 복제한 것들에 문제가 생길 수 있다.
- 그걸 해결하려면 재귀로 clone을 호출해야한다.
    - 하지만 필드가 final이면 새로운 값을 할당할 수 없어서 final을 잠시 풀어야 한다.
    - → 일반용법(가변객체를 참조하는 필드는 final로 선언하라)과 충돌한다.

```java
@Override
public Stack clone() {
	try {
		Stack result = (Stack)super.clone();
		result.elements = elements.clone();
		return result;
	} catch (CloneNotSupportedException e) {
		throw new AssertionError();
	}
}
```

### 💣 복사 생성자와 복사 팩터리를 사용하자.

: 복제를 원하면 복사 생성자나 복사 팩터리를 사용하고 배열만 clone 메소드 방식을 사용한다.

- 복사 생성자

    ```java
    public Yum(Yum yum) {...}
    ```

- 복사 팩터리

    ```java
    public static Yum newInstance(Yum yum) {...}
    ```
