## 79. 과도한 동기화는 피하라.

### 🧭 과도한 동기화는 성능을 떨어뜨리고 교착생태로 빠뜨리거나 예측할 수 없는 동작을 낳는다.

- `응답불가`와 `안전실패`를 피하려면 동기화 메서드나 동기화 블록 안에서는 제어를 절대로 클라이언트에게 양도하면 안된다.
  - 재정의할 수 있는 메소드를 호출하면 안된다.
  - 클라이언트가 넘겨준 함수 객체를 호출해서도 안된다.

### 🧭 예시

```java
public class ObservableSet<E> extends ForwardingSet<E> {

    public ObservableSet(Set<E> set) {
        super(set);
    }

    private final List<SetObserver<E>> observers = new ArrayList<>();

    public void addObserver(SetObserver<E> observer) {
        synchronized (observers) {
            observers.add(observer);
        }
    }
    
    public boolean removeObserver(SetObserver<E> observer) {
        synchronized (observers) {
            return observers.remove(observer);
        }
    }
    
    private void notifyElementAdded(E element) {
        synchronized (observers) {
            for(SetObserver<E> observer : observers) {
                observer.added(this, element);
            }
        }
    }
    
    @Override
    public boolean add(E element) {
        boolean added = super.add(element);
        if(added) {
            notifyElementAdded(element);
        }
        return added;
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        boolean result = false;
        for (E element : c) {
            result |= add(element); //notifyElementAdded를 호출
        }
        return result;
    }
}
```

: `Set`을 감싼 래퍼 클래스이고, 이 클래스의 클라이언트는 집합에 원소가 추가되면 알림을 받을 수 있다.

- 해당 메소드에 특정 숫자(23)가 되면 자기 자신을 삭제하는 로직을 추가한다.

```java
public static void main(String[] args) {
	ObservableSet<Integer> set = new ObservableSet<>(New HashSet<>());
	
	set.addObserver(new SetObserver<Integer>() {
		public void added(ObservableSet<Integer> s, Integer e) {
			System.out.println(e);
			if (e == 23) s.removeObserver(this); // 23이면 삭제
		}
	});

	for (int i = 0; i < 100; i++) 
		set.add(i);
}
```

: 실제로 진행하면 23까지 출력 후에 `ConcurrentModificationException` 을 터뜨린다.

- 순회하는 중간에 제거하는 것은 허용되지 않은 동작이다.
- 순회하는 것은 동기화 블록 안에 있으니까 동시수정이 일어나지 않도록 보장하지만, 콜백을 거쳐 되돌아와 수정하는 것까지는 막지 못한다.

- 새로운 예외발생

```java
set.addObserver(new SetObserver<Integer>() {
    public void added(ObservableSet<Integer> s, Integer e) {
        System.out.println(e);
        if (e == 23) {
            ExecutorService exec = Executors.newSingleThreadExecutor(); // 새로운 스레드
            try {
                exec.submit(() -> s.removeObserver(this)).get();
            } catch (ExecutionException | InterruptedException ex) {
                throw new AssertionError(ex);
            } finally {
                exec.shutdown();
            }
        }
    }
});
```

: 교착상태에 빠진다.

- 스레드1이 락을 잡을채로 순회한다.
- 스레드2는 삭제하려하나 락이 잡혀있어서 삭제가 안된다.
- 스레드1은 락을 잡고 있으면서 스레드2가 삭제하기를 기다린다.

### 🧭 해결방법

- 메소드 호출을 동기화 블록 바깥으로 옮긴다.

```java
private void notifyElementAdded(E element) {
    List<SetObserver<E>> snapshot = null;
    synchronized(observers) {
        snapshot = new ArrayList<>(observers);
    }
    for (SetObserver<E> observer : snapshot)
        observer.added(this, element);
}
```

- `CopyOnWriteArrayList` 를 이용한다.
  - `CopyOnWriteArrayList` 가 정확히 이 목적으로 설계되었다.
- ArrayList를 구현한 클래스로 내부를 변경하는 작업은 항상 깨끗한 복사본을 만들어 수행하도록 구현했다.
- 락이 필요없어 매우 빠르다.
- 수정할 일이 드물고 순회만 빈번히 일어나는 관찰자 리스트 용도로는 최적이다.

### 🧭 기본 규칙은 동기화 영역에서는 가능한 한 일을 적게 하는 것이다.

- 성능측면
  - 병렬로 실행할 기회를 잃고, 모든 코어가 메모리를 일관되게 보기 위한 지연시간이 진짜 비용이다.
- 가변클래스를 만들려면?
  1. 동기화를 하지말고, 그 클래스를 동시에 사용해야하는 클래스가 외부에서 알아서 동기화하게 하자.
  2. 동기화를 내부에서 수행해 스레드 안전한 클래스로 만들자
    1. 클라이언트가 외부에서 객체 전체에 락을 거는 것보다 동시성을 월등히 개선할 수 있을때는 → 2번