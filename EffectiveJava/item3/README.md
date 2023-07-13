## 3. private 생성자나 열거 타입으로 싱글턴임을 보증하라.

싱글턴: 객체의 인스턴스를 하나만 생성해서 사용한다.

- 싱글턴 생성방식 1- 필드방식

    ```java
    public class Singleton {
    	public static final Singleton INSTANCE = new Singleton();
    
    	private Singleton() { } // private으로 기본생성자 만들어서 다른 곳에서도 생성못하게 한다. 
    
    	...
    }
    ```

- 싱글턴 생성방식 2- 정적 팩터리 방식

    ```java
    class Singleton {
    	//static을 통해 class가 로드될때 객체를 생성 
    	private static Singleton singleton = new Singleton(); 
    	
    	private Singleton() {} // private으로 기본생성자 만들어서 다른 곳에서도 생성못하게 한다. 
    	
    	public static Singleton getInstance() {
    		return singleton;
    	}
    }
    ```

- 싱글턴 생성방식 3- enum으로 만든다.

    ```java
    public enum Singleton {
    	SINGLE; 
    	
    	public String getName() {
    		return "Single";
    	}
    
    }
    ```


→ 싱글턴으로 생성하면 이를 사용하는 클라이언트를 테스트하기가 어려울수있다. <br>
→ 인스턴스가 하나이기 때문에 해당 인스턴스를 이용하는 클라이언트들끼리 뭉쳐있고 그래서 유닛테스트를 하기 힘들다.