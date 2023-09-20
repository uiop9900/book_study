## 88. readObject 메소드는 방어적으로 작성하라.

### 🧨 readObject 메서드는 실질적으로 또 다른 public 생성자이다.

```java
// 방어적 복사를 사용하는 불변 클래스
public final class Period {
    private final Date start;
    private final Date end;

    /**
     * @param start 시작 시각
     * @param end 종료 시각; 시작 시각보다 뒤여야 한다.
     * @throws IllegalArgumentException 시작 시각이 종료 시각보다 늦을 때 발생한다.
     * @throws NullPointerException start나 end가 null이면 발행한다.
     */
    public Period(Date start, Date end) {
        this.start = new Date(start.getTime());
        this.end = new Date(end.getTime());
        if(this.start.compareTo(this.end) > 0) {
            throw new IllegalArgumentException(start + "가 " + end + "보다 늦다.");
        }
    }

    public Date start() { return new Date(start.getTime()); }
    public Date end() { return new Date(end.getTime()); }
    public String toString() { return start + "-" + end; }
}
```

: 논리적 표현과 물리적 표현이 부하해 기본 직렬화를 써도 괜찮아 보인다. → `implements Serializable` 하기

- 하지만 그렇게되면 불변식을 보장하기 힘들다.
- `readObject` 메서드가 실질적으로 또 다른 public 생성자다른 생성자와 똑같은 수준으로 주의를 기울여야 한다.
- 보통의 경우 바이트 스트림은 정상적으로 생성된 인스턴스를 직렬화해 만들어진다.
- 하지만 불변식을 깨뜨릴 의도로 임의 생성한 바이트 스트림을 건네면 정상적인 생성자로는 만들어낼 수 없는 객체가 생성된다.

### 🧨 해결방법

- `readObject`가 `defaultReadObject`를 호출한 다음 역직렬화된 객체가 유효한지 검사해야한다.

### 🧨 새로운 문제점

- 정상 인스턴스에서 시작된 바이트 스트림 끝에 private Date의 필드로의 참조를 추가해 가변 인스턴스로 만든다.
- 공격자는 악의적인 객체 참조를 읽어서 객체의 내부 정보를 얻을 수 있다.

```java
// 가변 공격의 예
public class MutablePeriod {
    //Period 인스턴스
    public final Period period;

    //시작 시각 필드 - 외부에서 접근할 수 없어야 한다.
    public final Date start;
    //종료 시각 필드 - 외부에서 접근할 수 없어야 한다.
    public final Date end;

    public MutablePeriod() {
        try {
            ByteArrayOutputStream bos = new ByteArrayOutputStream();
            ObjectArrayOutputStream out = new ObjectArrayOutputStream(bos);

            //유효한 Period 인스턴스를 직렬화한다.
            out.writeObject(new Period(new Date(), new Date()));

            /**
             * 악의적인 '이전 객체 참조', 즉 내부 Date 필드로의 참조를 추가한다.
             * 상세 내용은 자바 객체 직렬화 명세의 6.4절을 참고하자.
             */
            byte[] ref = {0x71, 0, 0x7e, 0, 5}; // 참조 #5
            bos.write(ref); // 시작 start 필드 참조 추가
            ref[4] = 4; //참조 #4
            bos.write(ref); // 종료(end) 필드 참조 추가

            // Period 역직렬화 후 Date 참조를 훔친다.
            ObjectInputStream in = new ObjectInputStream(new ByteArrayInputStream(bos.toByteArray()));
            period = (Period) in.readObject();
            start = (Date) in.readObject();
            end = (Date) in.readObject();
        } catch (IOException | ClassNotFoundException e) {
            throw new AssertionError(e);
        }
    }
}
```

```java
// 공격이 실제로 이뤄지는 모습
public static void main(String[] args) {
    MutablePeriod mp = new MutablePeriod();
    Period p = mp.period;
    Date pEnd = mp.end;

    //시간을 되돌리자!
    pEnd.setYear(78);
    System.out.println(p);

    //60년대로 회귀!
    pEnd.setYear(60);
    System.out.println(p);
}
```

: 불변식은 유지되었으나, 의도적으로 내부의 값을 수정할 수 있다.

→ **객체를 역직렬화 할 때는 클라이언트가 소유해서는 안되는 객체 참조를 갖는 필드를 모두 반드시 방어적으로 복사해야 한다.**

- 불변 클래스안의 private한 요소들을 방어적으로 복사한다.

```java
private void readObject(ObjectInputStream s) throws IOException, ClassNotFoundException {
    s.defaultReadObject();

    // 가변 요소들을 방어적으로 복사한다.
    start = new Date(start.getTime());
    end = new Date(end.getTime());

    // 불변식을 만족하는지 검사한다.
    if(start.compareTo(end) > 0) {
       throw new InvalidObjectException(start + "가 " + end + "보다 늦다.");
    }
}
```

: 방어적으로 복사하고 유효성 체크를 한다.

- final 필드는 방어적 복사가 불가능하다. → `readObject` 메서드를 사용하려면 `start`와 `end`필드에서 final 한정자를 제거한다.

### 🧨 readObject를 사용할지 판단하는 방법

- 모든 필드의 값(`transient` 필드를 제외)을 매개변수로 받아 유효성 검사 없이 필드에 대입하는 public 생성자를 추가해도 괜찮은가?
  - `아니오` → 커스텀 `readObject`를 만들어 (생성자에서 수행했어야 할) 모든 유효성 검사와 방어적 복사를 수행, 혹은 직렬화 프록시 패턴(아이템 90)을 사용한다.


### 🧨 정리

- `readObject` 메서드를 작성할 때는 언제나 public 생성자를 작성하는 자세로 임해야 한다.
- `readObject`는 어떤 바이트 스트림이 넘어오더라도 유효한 인스턴스를 만들어내야한다.
- 바이트 스트림이 진짜 직렬화된 인스턴스라고 가정해서는 안 된다.
- 이번 아이템에서는 기본 직렬화 형태를 사용한 클래스로 예시를 들었지만 커스텀 직렬화를 사용하더라도 모든 문제가 그대로 발생할 수 있다.