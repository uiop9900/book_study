## 6. 불필요한 객체 생성을 피하라.

```java
String s1 = new String("Java"); // 매번 새롭게 생성
String s2 = "Java"; // 하나의 인스턴스를 재사용
Boolean(String) -> Boolean.valueOf(String)
```

- 팩터리 메소드는 가변객체라도 사용중에 변경되지 않는다면 재사용할 수 있다.

- `String.match`는 문자열 형태를 확인하는데 좋지만, 한번만 쓰이고 버려지는 대상이다.<br> → 생성비용이 비싸다. <br>→ 이런 경우, match를 호출하지 않고, 따로 해당 코드를 내 class에서 재선언해서 캐싱해두고 해당 인스턴스를 재사용한다. <br>→ 속도도 빨리지고 메소드명도 의도에 맞게 지을 수 있어서 좋다.


- 박싱과 언박싱
 ```
- 원시타입: int, char, byte, short, float, double, long, boolean
        - stack에 값을 저장
- 참조타입(wrapper class): Integer, Character, Byte, Short, Float, Double, Long, Boolean
        - stack에 주소 저장하고 heap에 참조값 저장
        - 참조된 주소값으로 비교하기때문에 `==` 이 아닌 `equals`로 비교해야한다.
        - null을 가질 수 있다.
        - 접근시간이 더 오래 걸리고 메모리도 더 쓴다.
- `Boxing(박싱)`: 원시 타입 → 참조 타입 변환
- `Unboxing(언박싱)`: 참조 타입 → 원시 타입 변환
 ``` 

- 오토박싱
  - 원시와 참조를 섞어서 쓰면 알아서 오토박싱을 해준다.
  - 되도록 기본타입을 사용하고 오토박싱 되지않게 유의한다.

그렇다고 해서, 객체생성이 비싸니 함부로 생성하지 않는다 → **X** <br>
기존 객체를 사용한다면 새로운 객체를 만들지 말자! → **O**

방어적 복사가 필요한 상황에서 객체를 재사용했을 때 **>** 피해 객체를 반복 생성과 사용했을때 피해