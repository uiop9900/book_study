## 23. 태그 달린 클래스보다는 클래스 계층구조를 활용하라.

### 태그란?

클래스가 어떤 타입인지에 대한 정보를 담고 있는 변수(필드)를 의미한다.

```java
// 태그 달린 클래스
class Figure {
    enum Shape {RECTANGLE, CIRCLE};

    // 어떤 모양인지 나타내는 태그 필드
    final Shape shape;

    // 태그가 RECTANGLE일 때만 사용되는 필드들
    double length;
    double width;

    // 태그가 CIRCLE일 때만 사용되는 필드들
    double radius;

    // 원을 만드는 생성자
    Figure(double radius) {
        shape = Shape.CIRCLE;
        this.radius = radius;
    }

    // 사각형을 만드는 생성자
    Figure(double length, double width) {
        shape = Shape.RECTANGLE;
        this.length = length;
        this.width = width;
    }

    double area() {
        switch (shape) {
            case RECTANGLE:
                return length * width;

            case CIRCLE:
                return Math.PI * (radius * radius);

            default:
                throw new AssertionError(shape);
        }
    }
}
```

- 쓸데없는 코드가 많다.
- 가독성도 나쁘고 메모리도 많이 사용한다.
- 쓰이지 않는 필드들까지 생성자에서 초기화해야한다.

→ 태그가 달린 클래스는 장황하고 오류를 내기 쉬우며 비효율적이다.

→ 태그 달린 클래스는 클라스 계층구조를 어설프게 흉내낸 아류이다.

### 💎 서브타이핑

```java
abstract class Figure {
		// 동작이 동일한 메소드들
		// 값이 동일한 필드들
    abstract double area();
}

class Circle extends Figure {
    final double radius;

    public Circle(double radius) {
        this.radius = radius;
    }

    @Override
    double area() {
        return Math.PI * (radius * radius);
    }
}

class Rectangle extends Figure {
    final double length;
    final double width;

    public Rectangle(double length, double width) {
        this.length = length;
        this.width = width;
    }

    @Override
    double area() {
        return length * width;
    }
}
```

- 루트가 될 추상클래스를 정의
  - `Figure`
- 태그 값에 따라 동작이 달라는 메소드들을 루트 클래스의 추상메소드로 정의한다.
  - `Figure:area`
- 그와 상관없이 동작, 값이 일정한 것들은 루트 클래스에 일반 메소드로 넣는다.