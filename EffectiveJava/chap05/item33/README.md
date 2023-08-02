## 33. 타입 안전 이종 컨테이너를 고려하라.

:`ThreadLocal<T>`, `AtomicReference<T>` 등의 단일 원소 컨테이너에도 흔히 쓰인다.

여기서는 컨테이너 자신이 매개변수화 된다.

→ 컨테이너 대신 키를 매개변수화한 다음, 매개변수화한 키를 함께 제공한다.

타입토큰(type token): 메서드들이 주고받는 class리터럴을 뜻한다.

```java
public static void main(String[] args) {
	Favorites f = new Favorites();
	
	f.putFavorite(String.class, "Java");
	f.putFavorite(Integer.class, 0xcafebabe);
	f.putFavorite(Class.class, Favorites.class);

	String favoriteString = f.getFavorite(String.class);
	int favoriteInteger = f.getFavorite(Integer.class);
	Class<?> favoriteClass = f.getFavorite(Class.class);

	System.out.printf("%s %x %s\\\\n", favoriteString, favoriteInteger, favoriteClass.getName());
}
```

### 💡favorite class 예시

```java
public class Favorite {
	private Map<Class<?>, Object> favorites = new HashMap<>();

	public <T> void putFavorite(Class<T> type, T instance) {
		favorites.put(Objects.requireNonNull(type), type.cast(instance));
	}
	public <T> T getFavorite(Class<T> type) {
		return type.cast(favorites.get(type));
	}
}
```

- `Map<Class<?>, Object>` : 와일드카드 타입이 중첩되었다.
  - 키 자체가 와일드 카드 → 무엇이든 들어갈 수 있다.

  - 키마다의 다른 객체를 넣을 수 있다.

      ```java
      public static void main(String[] args) {
          Favorites f = new Favorites();
          
          f.putFavorite(String.class, "Java");
          f.putFavorite(Integer.class, 0xcafebabe);
          f.putFavorite(Class.class, Favorites.class);
      
          String favoriteString = f.getFavorite(String.class);
          int favoriteInteger = f.getFavorite(Integer.class);
          Class<?> favoriteClass = f.getFavorite(Class.class);
      
          System.out.printf("%s %x %s\\\\n", favoriteString, favoriteInteger, favoriteClass.getName());
      }
      ```


### 💡favorite class의 제약

- 악의적인 클라이언트가 Class 객체를 (제네릭이 아닌) `로(raw) 타입`(아이템 26)으로 넘기면 Favorite 인스턴스의 `타입 안전성`이 쉽게 깨진다. → 컴파일 시에 에러가 난다.
- Favorites 클래스의 두 번째 제약은 `실체화 불가 타입`에는 사용할 수 없다는 것이다.
  - String, String[]에는 저장이 가능해도, List<String>에는 저장이 불가하다.
  - `List<String>`용의 class객체를 얻을 수 없기때문이다.
  - `List<String>.class` 와 `List<Integre>.class` 는 같은 class 객체를 공유한다.
  - 이때 한정적 타입토큰을 이용하면 사용이 가능하다.
  - : 단순히 한정적 타입 매개변수나 한정적 와일드카드를 사용해서 표현이 가능한 타입을 제한하는 타입 토큰
- asSubClass 메소드 → 호출된 인스턴스 자신의 Class 객체를 인수가 명시한 클래스로 형변환한다.

```java
static Annotation getAnnotation(AnnotatedElement element,
																String annotationTypeName) { 
	Class<?> annotationType = null; // 비한정적 타입 토큰
	try {
		annotationType = Class.forName(annotationTypeName);
	} catch (Exception ex) {
		throw new IllegalArgumentException(ex);
	} 
	return element.getAnnotation(
		annotationType.asSubclass(Annotation.class));
}
```

d