## 1. 생성자 대신 정적 팩토리 메소드를 고려하라.

### 여기서 정적 팩토리 메소드란?
: 객체 생성의 역할을 하는 클래스(static)

객체를 생성할때 보통 `Car car = new Car();` 를 통해 `new`의 방법으로 생성하는데 직접 `new`를 하지않고 static 메소드 내부의 `new`를 이용해서 생성하는 방법이다.

```java
// String.java의 valueOf
public static String valueOf(char data[]) {
        return new String(data);
    }
```

### 🟢 장점 👍🏻

- 함수명을 가질 수 있다.

- 호출할때마다 새로운 객체를 생성할 필요가 없다.

    - static이기때문에 캐싱되어서 사용된다.
- 하위자료형 객체를 반환할 수 있다. → 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.

    - 상위 `car`, 하위 `suv` `sport` `sedan`

    ```java
    public static Car of(int score) {
        if (money > 200) {
          return new Sedan();
        } else if (money > 100 && money <= 200 ) {
          return new Sport();
        } else {
          return new Suv();
        }
      }
    ```

- 정적 팩토리 메소드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.

    - 인터페이스나 클래스는 만들어지는 시점에는 객체가 존재하지 않아도 된다. 추후 구현시에 필요함

    ```java
    public class Car {
    	// CarSeller를 인터페이스로 생성 -> 구현체가 없음 
        public static List<CarSeller> getCars() {
            return new ArrayList<>();
        }
    }
    ```


### 🔴 단점 👎🏻

- 정적 메소드는 static으로 선언하기때문에 상속하려면 public, protected로 선언되어야 해서 하위클래스를 만들 수 없다.
- 개발자가 만든 정적메소드는 찾기 힘들다. → 공유, api문서를 잘 쓰면 가능할수도!

### 명명방식

- from : 하나의 매개변수를 받아 해당 타입의 인스턴스를 반환하는 형변환 메서드
- of : 여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메서드
- valueOf : from과 Of의 더 자세한 버전
- instance 혹은 getInstance : 매개변수로 명시한 인스턴스를 반환하지만, 같은 인스턴스임을 보장 하지 않음
- create 혹은 newInstance :instance 혹은 getInstance 와 같으나 매번 새로운 인스턴스를 생성해 반환 함을 보장.