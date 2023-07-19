## 16. public 클래스에서는 public 필드가 아닌 접근자 메소드를 사용하라.

- 아래와 같이 절대 public으로 필드를 선언하지 않는다.

```java
class Point { 
	public double x;
	public double y;
}
```

- public 클래스로 패키지 바깥에서 접근할 수 있으면 접근자(get메소드)를 제공해서 유연성을 얻는다.

```java

class Point { 
	private double x;
	private double y;
}

// 생성자
public Point(double x, double y) {
        this.x = x;
        this.y = y;
    }

// getter
    public double getX() {
        return x;
    }

    public double getY() {
        return y;
    }

// setter
    public void setX(double x) {
        this.x = x;
    }

    public void setY(double y) {
        this.y = y;
    }

```

### 💎 public 클래스의 필드를 직접 노출한 사례

- java.awt.package 패키지의 Point, Dimension 클래스 → 절대 이렇게 하면 안된다.

### 💎 불변객체라면?

: 불변이라면 노출해도 되지만, api를 변경하지 않고서는 표현 방식을 바꿀 수 없다.

필드를 읽을 때 부수 작업을 수행할 수 없다는 단점이 있으나, 불변식은 보장할 수 있다.

package-private 클래스나 private 중첩 클래스면 필드를 노출하는게 나을 수도 있다.