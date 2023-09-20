## 90. 직렬화 인스턴스 대신 직렬화 프록시 사용을 검토하라.

### 🧨 직렬화 프록시 패턴

: Serializable을 구현하기로 한 순간부터 버그와 보완 위험이 크다. → 이를 해결하는 기법

1. 바깥 클래스의 논리적 상태를 정밀하게 표현하는 중첩 클래스를 설계해 `private static`으로 선언한다.
  1. 이 중첩 클래스가 바깥 클래스의 직렬화 프록시이다.
2. 중첩 클래스의 생성자는 단 하나여야 하며, 바깥 클래스를 매개변수로 받는다.
3. 생성자는 단순히 인수로 넘어온 인스턴스를 복사한다. (일관성 검사 및 방어적 복사도 필요 없다.)
4. 바깥 클래스와 직렬화 프록시 모두 `Serializable`을 선언한다.
5. 바깥 클래스에 `writeReplace` 메서드를 추가한다.

```java
class Period implements Serializable { // 직렬화
    private final Date start;
    private final Date end;

    public Period(Date start, Date end) {
        this.start = start;
        this.end = end;
    }

    // Peirod의 직렬화 프록시
    private static class SerializationProxy implements Serializable { // 직렬화
        private final Date start;
        private final Date end;

        public SerializationProxy(Period p) {
            this.start = p.start;
            this.end = p.end;
        }

            private static final long serialVersionUID = 234098243823485285L; // 아무 값이나 상관없다.
	    }
	}
}

/**
     * 바깥 클래스의 인스턴스를 직렬화 프록시로 변환한다.
     * 바깥 클래스의 직렬화된 인스턴스를 생성할 수 없다.
     */
    private Object writeReplace() {
        return new SerializationProxy(this);
    }
```

: `writeReplace` 가 바깥 클래스의 인스턴스 대신 `SerializationProxy` 를 반환하는 역할을 한다.

→ 직렬화가 이루어지기 전에 바깥 클래스의 인스턴스를 `직렬화 프록시`로 변환해준다.

### 🧨 직렬화 프록시의 장점

- 가짜 바이트 스트림 공격이나 내부 필드 탈취 공격을 프록시 수준에서 차단할 수 있다.
- 필드를 `final`로 선언해도 되므로 직렬화 시스템에서 진짜 불변을 만들 수 있다.
- 역직렬화 시 유효성 검사를 수행하지 않아도 된다.
- 역직렬화한 인스턴스와 원래의 직렬화된 클래스가 달라도 정상적으로 동작한다.
- 예시 - `EnumSet`

→ 특히, 역직렬화한 인스턴스와 원래의 직렬화된 클래스가 달라도 정상적으로 동작한다는 부분이 직렬화 프록시가 가지는 강력한 장점이다.

### 🧨 직렬화 프록시의 한계

- 멋대로 확장할 수 있는 클래스에는 적용할 수 없다.
- 객체 그래프에 순환이 있는 클래스에 적용할 수 없다.