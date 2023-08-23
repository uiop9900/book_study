## 54. null이 아닌, 빈 컬렉션이나 배열을 반환하라.

### 🌳 null을 반환하는 코드

```java
private final List<Cheese> cheesesInStock = ...;

/**
 * @return 매장 안의 모든 치즈 목록을 반환한다.
 * 	단, 재고가 하나도 없다면 null을 반환한다.
 */
public List<Cheese> getCheeses() {
	return cheesesInStock.isEmtpy() ? null
		: new ArrayList<>(cheesesInStock);
}
```

: 값이 없다고 null을 반환하게 되면 null을 위한 후처리가 필요하다.

### 🌳 null을 반환하는게 빈 컨테이너를 만드는 것보다 좋다?

1. 새로운 배열의 할당이 성능 저하의 주범이라고 확인되지 않는 한, 성능차이는 미미하다.
2. 빈 컬렉션과 배열은 굳이 새로 할당하지 않고도 반환할 수 있다.

```java
private static final Cheese[] EMTPY_CHEESE_ARRAY = new Cheese[0];

public Cheese[] getCheeses() {	
	return cheesesInStock.toArray(EMTPY_CHEESE_ARRAY);
}
```

: 위와 같이 빈 배열을 선언해두면 매번 새롭게 생성하지 않고 해당 불변객체를 반환하면 된다.

→ null이 아닌 빈 배열이나 컬렉션을 반환하라.