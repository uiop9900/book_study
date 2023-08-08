## 36. 비트 필드 대신 EnumSet을 사용하라.

### 🕹️ 비트 필드

- 비트별 OR를 이용해서 여러 상수를 하나의 집합으로 모은다.

- 해석하기가 어렵다

- 미리 값을 예측해서 적절한 타입(`Int`, `Long`)을 선택해야 한다.

    ```java
    public class Text  {
         public static final int STYLE_BOLD            = 1 << 0;   // 1
         public static final int STYLE_ITALIC          = 1 << 1;   // 2
         public static final int STYLE_UNDERLINE       = 1 << 2;   // 4
         public static final int STYLE_STRIKETHROUGH   = 1 << 3;   // 8
    
        // 매개변수 styles는 0개 이상의 STYLE_ 상수를 비트별 OR한 값이다. 
        public void applyStyles(int styles)  { ... }
    
    }
    ```


### 🕹️ EnumSet 클래스

```java
import java.util.*;

// 코드 36-2 EnumSet - 비트 필드를 대체하는 현대적 기법 (224쪽)
public class Text {
    public enum Style {BOLD, ITALIC, UNDERLINE, STRIKETHROUGH}

    // 어떤 Set을 넘겨도 되나, EnumSet이 가장 좋다.
    public void applyStyles(Set<Style> styles) {
        System.out.printf("Applying styles %s to text%n",
                Objects.requireNonNull(styles));
    }

    // 사용 예
    public static void main(String[] args) {
        Text text = new Text();
        text.applyStyles(EnumSet.of(Style.BOLD, Style.ITALIC));
    }
}
```

- `EnumSet<Style>` 이 아니라, `Set<Style>`로 받은 이유는 최대한 인터페이스로 받는게 좋다.
  - 다른 Set구현체로 들어와도 처리가 가능하다.
- 불변 EnumSet은 만들 수 없다.