## 20. 추상 클래스보다는 인터페이스를 우선하라.

자바가 제공하는 다중 구현 메커니즘 종류

- 인터페이스
  - 인터페이스의 메소드를 잘 정의했다면 어떤 클래스를 상속했던 신경쓰지 않는다.
- 추상클래스
  - 구현하는 클래스는 반드시 추상클래스의 하위클래스 이어야한다.

→ 기존 클래스에도 손쉽게 새로운 인터페이스를 구현해넣을 수 있다.

: 인터페이스의 메소드를 `overide`하고 `implements 인터페이스` 하면 된다.

### 💎 인터페이스는 믹스인(mixin) 정의에 안성맞춤이다.

### 믹스인이란?

: 대상 타입의 주된 기능에 선택적 기능을 혼합 한다고 해서 믹스인이라고 칭한다.

- 클래스는 implements는 하나만 가능하고 계층구조에서는 믹스인을 삽입하기 위한 합리적 위치가 없다.

### 💎 인터페이스로 계층구조가 없는 타입 프레임워크를 만들 수 있다.

: 타입을 계층적으로 정의하면 구조적으로 잘 표현이 가능하지만, 실무에서는 계층을 엄격히 구분하기 힘들다.

```java
public interface Singer {
	 AudioClip Sing(Song s);
}

public interface SongWriter {
	 Song compose(int chartPosition);
}

public interface SingSongwriter extends Singer, SongWriter {
	AudioClip strum();
	void actSensitive();
}
```

- 위와 같이 모두 구현해도 문제가 없다.

### 💎 래퍼클래스와 인터페이스를 사용하면 기능을 향상시키는 안전하고 강력한 수단이 된다.

- 타입을 추상 클래스로 정의해두면 그 타입에 기능을 추가하는 방법은 상속뿐이다.
- 상속해서 만든 클래스는 래퍼 클래스보다 활용도가 떨어지고 깨지기는 더 쉽다.
- 디폴트 메소드
  - 인터페이스의 메소드 중 구현방법이 명백한 것이 있다면, 그 구현을 디폴트 메소드로 제공한다.
  - 많은 인터페이스가 `equals`와 `hashCode` 같은 Object의 메서드를 정의하고 있지만, 이들은 디폴트 메서드로 제공해서는 안 된다.
  - 또한 인터페이스는 인스턴스 필드를 가질 수 없고 public이 아닌 정적 멤버도 가질 수 없다(단, private 정적 메서드는 예외다).

### 💎 템플릿 메소드 패턴

: 인터페이스와 추상 골격구현 클래스를 함께 제공해 인터페이스와 추상클래스의 장점을 제공한다.

- 인터페이스로 타입을 정의하고 디폴트메소드를 제공한다.
- 골격 구현 클래스로 나머지 메소드들도 구현한다.
- 이런 형태로 하면 인터페이스를 구현하는데 필요한 일이 대부분 완료된다.
- 관례상 interface명: `Interface`면, 골격구현클래스명: `AbstractInterface`이다.

→ 인터페이스의 메소드가 모두 디폴트 혹은 기반메소드가 된다면 굳이 골격 구현 클래스를 생성할 이유는 없다.

- 예시(기존)

    ```java
    public interface Study{ 
        
        void 책_읽자(); 
        void 강의를_보자(); 
        void 사이드를_하자(); 
        void 매일루틴();
    }
    
    public class CodingStudy implements Study { 
        @Override 
        void 책_읽자() { 
            System.out.println("이펙티브 자바"); 
        }
    
        @Override 
        void 강의를_보자() { 
            System.out.println("토비의 스프링"); 
        }
    
        @Override 
        void 사이드를_하자() { 
            System.out.println("아하포인트"); 
        }
    
        @Override
        void 매일루틴() {
            책_읽자();
            강의를_보자();
            사이드를_하자();
        }
    
    }
    
    public class JavaStudy implements Study {
        @Override
        void 책_읽자() {
            System.out.println("이펙티브 자바");
        }
    
     @Override 
        void 강의를_보자() { 
            System.out.println("토비의 스프링"); 
        }
    
        @Override 
        void 사이드를_하자() { 
            System.out.println("사이드2"); 
        }
    
        @Override
        void 매일루틴() {
            책_읽자();
            강의를_보자();
            사이드를_하자();
        }
    }
    ```

  - `CodingStudy`와 `JavaStudy`에서 `사이드를_하자` 메소드만 빼고 다 중첩이다.

- 인터페이스와 골격구현클래스로 변경

    ```java
    public interface Study {
    
        void 책_읽자(); 
        void 강의를_보자(); 
        void 사이드를_하자(); 
        void 매일루틴();
    }
    
    public abstract class AbstractStudy {
        @Override
        void 책_읽자() {
            System.out.println("이펙티브 자바");
        }
    
    	  @Override 
        void 강의를_보자() { 
            System.out.println("토비의 스프링"); 
        }
    
        @Override
        void 매일루틴() {
            책_읽자();
            강의를_보자();
            사이드를_하자();
        }
    }
    
    public class CodingStudy extends AbstractStudy implements Study {
    
        @Override 
        void 사이드를_하자() { 
            System.out.println("아하포인트"); 
        }
    }
    
    public class JavaStudy extends AbstractStudy implements Study {
    
        @Override 
        void 사이드를_하자() { 
            System.out.println("사이드2"); 
        }
    }
    
    ```

- 골격구현은 기본적으로 상속해서 사용하는 걸 가정하기에 설계 및 문서화를 모두 따라야 한다.
- 골격구현은 가능한 한 인터페이스의 디폴트 메소드로 제공하여 인터페이스를 구현한 모든 곳에서 활용하도록한다.
- 단순 구현
  - 골격 구현의 작은 변동으로 상속을 위해 인터페이스를 구현했지만 추상클래스는 아닌걸 말한다.