## 30. 이왕이면 제네릭 메소드로 만들라.

### 💡 메소드

```java
public static Set Union(Set s1, Set s2) {
   Set result = new HashSet(s1);
   result.addAll(s2);
   return result;
}
```

다 제네릭으로 선언되어있음 `Set`

타입매개변수로 변환한다.

```java
public static <E> Set<E> union(Set<E> s1, Set<E> S2){ // input 변경
  Set<E> result = new HashSet<>(s1); // 반환값 변경
  result.addAll(s2);
  return result; 
}
```

### 💡 제네릭 싱글턴 팩토리 패턴

- 불변객체를 여러 타입으로 활용할 수 있게끔 해야한다. 제네릭은 런타임에 타입 정보가 소거되므로 하나의 객체를 어떤 타입으로든 매개변수화 할 수 있다.
- 요청한 타입매개변수에 맞게 매번 그 객체의 타입을 바꿔주는 정적 팩터리를 만들어야 한다.
  - 예시 1
    - `Collections.reverseOrder`, `Collections.emptySet`


![스크린샷 2023-08-02 오후 5.02.41.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/638d71e2-20ed-4bd4-8345-d55e59fe1f2e/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-08-02_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_5.02.41.png)

- 예시2

  - 항등함수: 입력 값을 수정없이 그래도 반환하는 특별한 함수

    ```java
    private static UnaryOperator<Object> IDENTITY_FN = (t) -> t;
    
    @SuppressWarnings("unchecked")
    public static <T> unaryOperator<T> identityFunction() {
    	return (UnaryOperator<T>) IDENTITY_FN;
    }
    ```

  - Object → T 로 형변환한다.
  - 항등함수라 넣은 대로 나오기 때문에 타입 안정성이 보장되고 `@SuppressWarnings("unchecked")` 를 넣을 수 있다.

### 💡 제네릭 타입 한정

: 자기 자신이 들어간 표현식을 사용하여 타입 매개변수의 허용 범위를 한정하는 것이다.

- 주로 타입의 자연적 순서를 정하는 `Comparable`과 같이 쓰인다.

    ```java
    public static <E extends Comparable<E>> E max(Collection<E> c){
    	if(c.isEmpty())
    		throw new IllegalArgumentException("컬렉션이 비어 있습니다.");
    	
        E result = null;
    	for (E e :  c)
    		if(result == null || e.compareTo(result) > 0)
    			result = Objects.requireNonNull(e);
        return result;
    }
    ```

- `<E extends Comparable<E>>` == 모든 타입 E는 자신과 비교할 수 있다.