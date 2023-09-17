## 81. wait와 notify보다는 동시성 유틸리티를 사용하라.

### 🧭 wait와 notify는 올바르게 사용하기가 까다로우니 고수준 동시성 유틸리티를 사용하자.

- 실행자 프레임워크
- 동시성 컬렉션
  - `List`, `Queue`, `Map` 같은 표준 컬렉션 인터페이스에 동시성을 가미해 구현한 고성능 컬렉션
  - 동기화를 각자의 내부에서 수행한다.
  - 동시성을 무력화하는 건 불가능하며, 외부에서 락을 추가로 사용하면 오히려 속도가 느려진다.
  - → 하나의 원자적 동작으로 묶는 `상태 의존적 수정` 메소드들이 추가되었다.
- 동기화 장치

### 🧭 Collections.ConcurrentHashMap을 사용한다.

: `Collections.synchronizedMap`을 `ConcurrentHashMap`으로 교체하는 것만으로도 동시성 애플리케이션의 성능을 개선할 수 있다.

```java
private static final ConcurrentMap<String, String> map =
        new ConcurrentHashMap<>();

public static String intern(String s) {
    // ConcurrentHashMap은 검색 기능에 최적화 되어있기 때문에 get을 이용하면 훨씬 빠르다.
    String result = map.get(s);

    if (result == null) {
        result = map.putIfAbsent(s, s);
        if (result == null) {
            result = s;
        }
    }
    return result;
}
```

### 🧭 동기화 장치

: 스레드가 다른 스레드를 기다릴 수 있게 하며 서로 작업을 조율할 수 있게 해준다.

- `CountDownLatch`
  - 생성자는 `int`를 받으며 메서드를 몇번 호출해야하 대기 중인 스레드들을 깨우는지 결정한다.
- `Semaphore`
- `Phaser` - 가장 강력한 동기화 장치

### 🧭 시간간격을 잴 때는 항상 System.nanoTime을 사용하자.

: 기존에 쓰던 `System.currentTimeMillis` 보다 훨씬 정밀하다.

### 🧭 새로운 코드라면 언제나 동시성 유틸리티를 써야 한다.

- `wait`(스레드가 어떤 조건이 충족되기까지 기다리는 경우 사용한다) 과 `notify`는 사용하지 않는다.
- `wait` 메소드를 사용할때는 반드시 대기 반복문 관용구를 사용하라.
- 반복문 밖에서는 절대로 호출하지 말자.
-

### 🧭 조건이 만족되지 않아도 스레드가 깨어날 수 있는 상황

- 스레드가 `notify`를 호출하여 대기 중인 스레드가 깨어나는 사이 다른 스레드가 Lock을 얻어 상태를 변경하는 경우
- 조건이 만족되지 않았지만 다른 스레드가 실수 혹은 악의적으로 notift를 호출하는 경우(외부에 공개된 동기화된 메서드 안에서 호출하는 wait는 모두 이 문제에 영향을 받는다.)
- 대기 중인 스레드 중 일부만 조건이 충족되어도 notifyAll을 호출하는 경우(notifyAll은 모든 스레드를 깨운다.)
- 대기 중인 스레드가 허위 각성(spurious wakeup) 한 경우 - notify 없이 깨어나는 경우

### 🧭 notify와 notifyAll

- notify
  - 스레드 하나만 깨운다.
- notifyAll
  - 모든 스레드를 깨운다.
- 깨어나야 하는 모든 스레드가 깨어나는 것을 보장하니 항상 정확한 결과를 얻는다.
- 모든 스레드가 같은 조건을 기다리고, 조건이 한 번 충족될때마다 단 하나의 스레드만 혜택을 받는다. → `notify`
- 관련없는 스레드가 실수로 혹은 악의적으로 wait를 호출하는 공격으로부터 보호할 수 있다. → `notifyAll`

→ `wait`, `notify`는 쓸 이유가 거의 없다.