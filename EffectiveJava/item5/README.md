## 5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라.

- 정적 유틸리티, 싱글톤
    - 주입받는 대상이 하나임을 가정, 의존할 객체를 직접 생성한다.
- 사용하는 자원에 따라 동작이 달라지는 클래스는 아예 인스턴스를 생성할때 필요한 자원을 넘겨주는 방식으로 한다.

    ```java
    /** 의존성 주입 */
    public class SpellChecker {
    
    	private final Lexicon dictionary;
    
    	public SpellChecker(Lexicon dictionary){ // 아예 생성할때 자원을 넘긴다.
    		this.dictionary = Objects.requireNonNull(dictionary);
    	}
    }
    
    ```
- 해당 클래스가 하나 이상의 자원에 의존하고, 그 의존하는 자원이 클래스의 동작에 영향을 준다면 싱글턴과 정적 유틸리티 클래스는 지향한다.

- 클래스가 직접 만들어서도 안된다. 생성자에게 넘긴다.


### 해당 패턴의 변형

: 생성자에 자원팩터리를 넘겨준다.

- 팩토리: 호출할 때 마다 특정 타입의 인스턴스를 반복해서 만들어주는 객체 → `팩터리 메소드 패턴`
- Car를 상속받는 무엇이든(`suv` `sport` `sedan` )받아서 CarStore를 생성할 수 있는 팩터리이다.
```java
   /** 해당 패턴의 변형 */
Public CarStore create(Supplier<? extends Car> car){
	return 
}
```