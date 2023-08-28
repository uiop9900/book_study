## 57. 지역변수의 범위를 최소화하라.

지역변수의 범위를 줄이면 코드 가독성과 유지보수성이 높아지고 오류 가능성은 낮아진다.

### 🎃 지역변수는 가장 처음 쓰일 때, 선언한다.

- 너무 미리 선언하면 코드가 어수선해진다.
- 거의 모든 지역변수는 선언과 동시에 초기화해야한다.
- `try-catch`의 경우, `try` 밖에서도 사용해야하는 변수라면, `try블록` 앞에서 선언한다.

### 🎃 반복변수의 값이 반복문이 종료된 뒤에도 써야한다면, while문보다는 for문을 사용한다.

```java
Iterator<Element> i = c.iterator();
while (i.hasNext()) {
	doSomething(i.next());
}

...

Iterator<Element> i2 = c2.iterator();
while (i.hasNext()) { // i로 변수를 잘못 넣었다.
	doSomething(i2.next());
}
```

: 두 번째 while문에서 변수를 잘못넣어도 지역변수i는 살아있기 때문에 코드가 작동한다. → 다만 기대값이 아니라서 나중에 언제 에러가 될지 모른다.

```java
for (int i = 0, n = expensiveComputation(); i < n; i++) {
	// 로직
}
```

: `i`과 `n`의 범위가 정확하게 일치한다. → `n`에다 값을 저장해서 반복해서 저장하지 않게 한다.

### 🎃 메소드를 작게 유지하고 한 가지의 기능에 집중한다.