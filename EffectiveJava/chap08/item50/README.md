## 50. 적시에 방어적 복사본을 만들라.

- 자바는 안전한 언어다.
- 네이티브 메소드를 사용하지 않는다.
- 메모리 충돌 오류에서 안전하다.
- 자바로 작성한 클래스는 불변식이 지켜진다.

→ 하지만 클라이언트가 불변식을 깨드리려 혈안이 되어있는 걸 가정하고 방어적으로 프로그래밍 한다.

### 🌳 예시 불변식 클래스

```java
public final class Period {
    private final Date start;
    private final Date end;

    public Period(Date start, Date end) {
        if (start.compareTo(end) > 0) {
            throw new IllegalArgumentException(start + "가 " + end + "보다 늦다.");
        }
        this.start = start;
        this.end = end;
    }

    public Date start() {
        return start;
    }

    public Date end() {
        return end;
    }
}
```

### 🌳 불변식 깨뜨리기

```java
public final class Period {
    // .. 코드 생략

    public static void main(String[] args) {
        Date start = new Date();
        Date end = new Date();
        Period period = new Period(start, end);
        end.setYear(78); // period의 내부를 수정했다!
    }
}
```

- `Date` 객체 자체를 새롭게 생성해서 `Set` 한다.
- `Date` 대신 불변의 인스턴스를 사용해야 한다.
- `Date`는 낡은 API이니 새로운 코드를 작성할 때는 더 이상 사용하면 안 된다.

### 🌳 방어적 복사본을 만든다.

```java
public final class Period {
    private final Date start;
    private final Date end;

    public Period(Date start, Date end) {
				// 복사본 만들고
        this.start = new Date(start.getTime());
        this.end = new Date(end.getTime());
				// 유효성 체크한다.
        if (start.compareTo(end) > 0) {
            throw new IllegalArgumentException(start + "가 " + end + "보다 늦다.");
        }
    }

    // 생략
}
```

- 메소드 안에서 객체를 생성하고 나서 유효성 검사를 한다.
- 멀티스레딩 환경이라면 원본 객체의 유효성을 검사한 후 복사본을 만드는 그 찰나의 취약한 순간에 다른 스레드가 원본 객체를 수정할 위험이 있기 때문이다.
- 방어적 복사를 매개변수 유효성 검사 전에 수행하면 이런 위험에서 해방될 수 있다. 컴퓨터 보안 커뮤니티에서는 이를 검사시점/사용시점(time-of-check/time-of-use) 공격 혹은 TOCTOU 공격이라 한다.

### 🌳 방어적 복사본 깨뜨리기

```java
public final class Period {

    // 코드 생략 ..

    public static void main(String[] args) {
        Date start = new Date(); // 가변필드
        Date end = new Date(); // 가변필드
        Period period = new Period(start, end);
        period.end().setYear(78); // period의 내부를 변경했다!
    }
}
```

- period 메소드도 public이기에 결국 변경이 가능하다.

→ **가변 필드의 방어적 복사본을 반환하면 된다.**

### 🌳 접근자를 보완한다.

```java
public final class Period {

    public Date start() {
        return new Date(start.getTime());
    }

    public Date end() {
        return new Date(end.getTime());
    }
}
```

- `period`만이 `start`와 `end`에 접근이 가능해졌다. → 불변.
- 방어적 복사에 `clone`을 사용해도 되지만, 일반적으로는 생성자나 정적 팩토리를 쓰는게 좋다.
- 길이가 1 이상인 배열은 무조건 가변이다. → 내부에서 사용하는 배열을 클라이언트에 반환할 때는 항상 방어적 복사를 수행해야 한다.

### 🌳 정리

- 클래스가 클라이언트로부터 받는 혹은 클라이언트로 반환하는 구성요소가 가변이라면 그 요소는 반드시 방어적으로 복사해야 한다.
- 복사 비용이 너무 크거나 클라이언트가 그 요소를 잘못 수정할 일이 없음을 신뢰한다면 방어적 복사를 수행하는 대신 해당 구성요소를 수정했을 때의 책임이 클라이언트에 있음을 문서에 명시하도록 한다.