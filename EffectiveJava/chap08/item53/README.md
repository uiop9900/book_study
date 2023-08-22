## 53. 가변인수는 신중히 사용하라.

### 🌳 가변인수란?

```java
static int sum(int... args) {
    int sum = 0;
    for (int arg : args) 
        sum += arg;
    return sum;
}
```

: 위와 같이 명시한 타입 `int` 의 인수를 0개 이상 받을 수 있는 것을 말한다.

- 인수 개수는 런타임에 배열 길이로 알 수 있다.

### 🌳 잘못 구현하면?

```java
static int min(int... args) {
    if (args.length == 0)
        throw new IllegalArgumentException("인수가 1개 이상 필요합니다.");
    int min = args[0];
    for (int i = 1; i < args.length; i++)
        if (args[i] < min)
            min = args[i];
    return min;
}
```

: 인수가 최소 1개 이상이어야 하는 상황이다.

- 인수가 0개인 경우, 해당 사실을 런타임에나 알 수 있다. (컴파일 시점에는 알 수 없다.)
- 코드도 지저분한다.

### 🌳 보완된 코드

```java
static int min(int firstArgs, int... remainingArgs) {
    int min = firstArg;
    for (int arg : remainingArgs)
        if (arg < min)
            min = arg;
    return min;
}
```

: 평범한 매개변수를 처음에 받고, 그 뒤에 가변인수로 받는다.

- 위의 2가지 문제점이 다 해결된다.

### 🌳 가변인수 메소드는 호출될때마다 배열을 새롭게 할당받고 초기화한다.

```java
public void foo() { }
public void foo(int a1) { }
public void foo(int a1, int a2) { }
public void foo(int a1, int a2, int a3) { }
public void foo(int a1, int a2, int a3, int... rest) { }
```

: 해당 메소드 호출의 95%가 인수를 3개 이하로 사용한다면 위와 같이 메소드를 다중정의해서 성능 최적화를 한다.

- 이미 정의되어있기때문에 새롭게 배열을 생성하지않고 해당 메소드가 호출된다.