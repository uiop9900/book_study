## 80. 스레드보다는 실행자, 태스크, 스트림을 애용하라.

### 🧭 java.util.concurrent 패키지

: 유연한 태스크 실행 기능을 담고 있다.

```java
// 작업 큐를 생성하다. 
ExcutorService exec  = Executors.newSingleThreadExcutor();

// 다음은 이 Excutor에 실행할 태스크(task; 작업)를 넘기는 방법이다.
exec.execute(runnable);

// 다음은 Excutor를 우아하게 종료시키는 방법이다(이 작업이 실패하면 VM 자체가 종료되지 않을 것이다)
exec.shutdown();
```

- 특정 태스크가 완료되기를 기다린다.
- 태스크 모음 중 아무것 하나(invokeAny 메서드) 혹은 모든 태스크(invokeALL 메서드)가 완료되기를 기다린다.
- 실행자 서비스가 종료하기를 기다린다(awaitTermination 메서드)
- 완료된 태스크들의 결과를 차례로 받는다(ExecutorCompletionService 이용)
- 태스크를 특정 시간에 혹은 주기적으로 실행하게 한다(ScheculedThread PoolExecutor 이용)

### 🧭 Executors.newCachedThreadPool

- 요청 받은 태스크들이 즉시 스레드에 위임되어 실행된다.
- 가용한 스레드가 없다면 새로 하나 생성한다.
- CPU가 100%가 된다. → 다운된다.

→ 무거운 프로덕션 서버에는 좋지 않다.

### 🧭 무거운 프로덕션에는?

: 스레드 개수를 개수를 고정한 `newFixedThreadPool` 이나 완전히 통제할 수 있는 `ThreadPoolExecutor`를 사용한다.

### 🧭 Runnable과 Callable

- 태스크: 작업 단위를 나타내는 핵심 추상 개념
- `Callable`은 값을 반환하고 임의의 예외를 던질 수 있다.
- 실행자 서비스: 태스크를 수행하는 일반적인 메커니즘
- 실행자 프레임워크가 작업 수행을 담당해준다.

### 🧭 ForkJoinTask

- `ForkJoinTask` 인스턴스는 작은 하위 태스크로 나뉠 수 있고 `ForkJoinPool`을 구성하는 스레드들이 이 태스크들을 처리하며, 일을 먼저 끝낸 스레드는 다른 스레드의 남은 태스크를 가져와 대신 처리할 수도 있다.
- 이러한 포크-조인 태스크를 직접 작성하고 튜닝하기란 어려운 일이지만, 포크-조인 풀을 이용해 만든 병렬 스트림을 이용하면 적은 노력으로 그 이점을 얻을 수 있다. 물론 포크-조인에 적합한 형태의 작업이어야 한다.