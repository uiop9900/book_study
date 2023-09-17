## 78. 공유 중인 가변 데이터는 동기화해 사용하라.

### 🧭 `Synchronized`

: 해당 메소드나 블록을 한번에 한 스레드씩 수행하도록 보장한다.

### 🧭 동기화란?

1) 한 스레드가 변경하는 중(상태가 일관되지 않는 순간)이면 다른 스레드가 해당 순간의 객체를 보지 못하게 막는 용도

- 한 객체가 일관된 상태로 생성
- 해당 객체에 접근하는 메소드는 그 객체에 락을 건다.
- 락을 건 메소드는 객체의 상태를 확인하고 필요하면 수정한다.

→ 객체를 하나의 일관된 상태에서 다른 일관된 상태로 변화시킨다.

→ 동기화를 제대로 사용하면 객체가 일관되지 않은 순간을 볼 수 없다.

2) 동기화 없이는 한 스레드가 만든 변화를 다른 스레드에서 확인하지 못할 수 있다.

- `long`, `double` 외의 변수를 읽고 쓰는 동작은 원자적이다.

= 항상 어떤 스레드가 정상적으로 저장한 값을 온전히 읽어오는 것을 보장한다.

### 🧭 배타적 실행뿐 아니라 스레드 사이의 안정적인 통신에 꼭 필요하다.

- 공유중인 가변 데이터를 원자적으로 읽고 쓸 수 있더라도 동기화를 실패하면 처참한 결과가 나온다.

```java
public class StopThread {
	private static boolean stopRequested;

	public static void main(String[] args) throws InterruptedException {
		Thread backgroundThread = new Thread(() -> {
			int i = 0;
			while (!stopRequested) 
				i ++;
		});
		backgroundThread.start();

		TimeUnit.SECONDS.sleep(1); // 1초 쉰다.
		stopRequested = true;
	}
}
```

- `Thread.stop`은 사용하지 말자.

  : 1 초 후에 `true`가 되면서 반복문을 빠져나올거라 예상하지만 실제로는 영원히 수행된다.

→ 동기화하지 않으면 메인 스레드가 수정한 값을 백그라운 스레드가 언제 보게 될지 알수 없다.

: `true`로 변경되어도 동기화가 되지 않아서 계속해서 수행된다.

- 동기화해서 스레드를 정상종료 시킨다.

```java
public class StopThread {
	private static boolean stopRequested;

	private static synchronized void requestStop() {
		stopRequested = true;
	}
	
	private static synchronized boolean stopRequested() {
		return stopRequested;
	}

	public static void main(String[] args) throws InterruptedException {
		Thread backgroundThread = new Thread(() -> {
			int i = 0;
			while (!stopRequested()) 
				i ++;
		});
		backgroundThread.start();

		TimeUnit.SECONDS.sleep(1);
		requestStop(); // 동기화해서 값을 받는다.
	}
}
```

: 메소드를 `synchronized` 해서 동기화한다.

### 🧭 쓰기와 읽기 모두가 동기화되지 않으면 동작을 보장하지 않는다.

1. `volatile`로 선언하면 동기화를 생략해도 된다.
- 항상 가장 최근에 기록된 값을 읽게 됨을 보장한다.

```java
public class StopThread {
	private static volatile boolean stopRequested; // volatile로 선언

	public static void main(String[] args) throws InterruptedException {
		Thread backgroudThread = new Tread(() -> {
			int i = 0;
			while (!stopRequested) 
				i ++;
		});
		backgroundThread.start();

		TimeUnit.SECONDS.sleep(1);
		stopRequested = true;
	}
}
```

1. 하지만 `volatile` 을 사용할때는 주의해서 사용해야 한다.

```java
private static volatile int nextSerialNumber = 0;

public static int generateSerialNumber() {
	return nextSerialNumber++;
}
```

- 여기서 ++ 되는 구간을 조심해야 한다.
  - ======== A 스레드 - 시작
  - ======== A 스레드 - 값 조회
  - ======== B 스레드 - 값 조회
  - ======== A 스레드 - 새로운 값 조회
  - 위와 같은 상황이면 A 스레드가 값을 변경해도 B스레드는 변경하기 전의 값을 조회하게 된다.

→ `안전실패`

1. `generateSerialNumber` 에 `synchronized`를 붙이면 해결된다.
  - 그 대신 필드에서 `volatile` 는 뺀다.
2. `Atomic`을 이용해서 선언한다.
  - 락 없이도 스레드 안전한 프로그래밍을 지원하는 클래스가 담겨있다.

```java
private static final AtomicLong nextSerialNumber = 0;

public static int generateSerialNumber() {
	return nextSerialNumber++;
}
```

### 🧭 가변 데이터는 단일 스레드에서만 쓰도록 한다.

- 안전발행
  - 데이터를 다 수정한 후 다른 스레드에 공유할 때는 해당 객체에서 공유하는 부분만 동기화해도 된다.
  - 다시 수정하기 전까지는 동기화없이 자유롭게 값을 읽어갈 수 있다. → 사실상 불변
  - 이런 객체를 건네는 행위
- 안전 발행을 하는 방법
  - 정적필드, volatile 필드, final 필드, 락을 통해 접근