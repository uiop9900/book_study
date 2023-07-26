## 25. 톱레벨 클래스는 한 파일에 하나만 담으라.

한 클래스안에 여러개의 값을 정의할 수 있지만, 어느 소스파일을 먼저 컴파일 하는지에 따라 달라진다.

- main 클래스

    ```java
    public class Main {
        public static void main(String[] args) {
            System.out.println(Utensil.NAME + Dessert.NAME);
        }
    }
    ```

- `Utensil.java` 에 정의된 클래스

    ```java
    class Utensil {
        static final String NAME = "pan";
    }
    
    class Dessert {
        static final String NAME = "cake";
    }
    ```

  - 실행하면 `pancake` 으로 잘 나온다.
- `Desert.java` 에 정의된 클래스

    ```java
    class Utensil {
        static final String NAME = "pot";
    }
    
    class Dessert {
        static final String NAME = "pie";
    }
    ```

  - 중복으로 정의되어있으므로 컴파일 에러가 난다.

### 💎 톱레벨 클래스들은 서로 다른 소스 파일로 분리한다.

- 굳이 한 클래스에 다 담고 싶다면 정적 멤버 클래스를 사용한다.

```java
public class Test {
    public static void main(String[] args) {
        System.out.println(Utensil.NAME + Dessert.NAME);
    }
    
    private static class Utensil{
        static final String NAME = "pan";
    }
    
    private static class Dessert{
        static final String NAME = "cake";
    }
}
```

