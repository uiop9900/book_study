## 72. 표준 예외를 사용하라.

### 😵‍💫 표준 예외를 사용하면 내 API가 다른 사람들이 익히고 사용하기 쉬워진다.

- 읽기 쉽다.
- 예외 클래스 수가 적을 수록, 메모리 사용량도 줄고 클래스를 적재하는 시간도 적게 걸린다.

### 😵‍💫 표준예외

- `IllegalArgumentException`
  - 호출자가 인수로 부적절한 값을 넘길때 던지는 예외
- `NullPointException`
  - null 값을 허용하지 않는 경우 던지는 예외
- `IndexOutBoundsException`
  - 시퀀스의 허용 범위를 넘는 경우
- `ConcurrentModificationException`
  - 단일 스레드에서 사용하려고 설계한 객체를 여러 스레드에서 동시에 수정하려고 할 때 던지는 예외
- `UnsupportedOperationException`
  - 요청한 동작을 대상 객체가 지원하지 않는 경우에 던지는 예외

| 예외 | 주요 쓰임 |
| --- | --- |
| IllegalArgumentException | 허용하지 않는 값이 인수로 건네졌을 때 (null은 NPE로 처리) |
| IllegalStateException | 객체가 메서드를 수행하기 적절하지 않은 상태일 때 |
| NullPointerException | null을 허용하지 않는 메서드에 null을 건넸을 때 |
| IndexOutOfBoundsException | 인덱스가 범위를 넘어섰을 때 |
| ConcurrentModificationException | 허용하지 않는 동시 수정이 발견됐을 때 |
| UnSupportedOperationException | 호출한 메서드를 지원하지 않을 때 |

### 😵‍💫 Exception, RuntimeException, Throwable, Error는 직접 재사용하지 않는다.

### 😵‍💫 항상 표준 예외를 재사용하자.

- 예외는 직렬화가 불가하다. → 나만의 예외를 새로 만들지 않는다.
- 인수 값이 무엇이든 어차피 실패 → `IllegalStateException`, 그렇지 않으면 `IllegalArgumentException`