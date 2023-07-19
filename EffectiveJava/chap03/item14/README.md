## 14. Comparable을 구현할지 고려하라.

: compareTo는 Object의 메소드가 아니며, equals와 같다.

- 동치성과 순서까지 비교할 수 있으며 제네릭 하다.
- Comparable을 구현했다 == 순서가 있다 == 정렬이 가능하다.
- → 순서가 명확한 값 클레스를 작성한다면 Comparable를 구현하자.

### 💡CompareTo 메서드의 일반규약

: equals와 비슷하며 해당 클래스들은 Comparable를 구현한 것으로 전제한다.

단, compareTo는 타입이 다른 객체는 신경쓰지 않는다. 타입 불일치 시 `ClassCastException`을 반환한다.

```
1. 참조의 순서를 바꿔도 예상한 결과가 나와야 한다.
	- (x.compartTo(y)) == -(y.compareTo(x)) : x가 y보다 크면 y는 x보다 작다.
2. x가 y보다 크고, y가 z보다 크면 x는 z보다 크다.
	- (x.compareTo(y) > 0 && y.compareTo(z) > 0)이면 x.compareTo(z) > 0이다.
3. 크기가 같은 객체들끼리는 어떤 객체와 비교하더라도 항상 같아야 한다.
	- x.compareTo(y) == 0이면 sgn(x.compareTo(z)) == sgn(y.compareTo(z))
4. (x.compareTo(y) == 0) == (x.equals(y)) 는 권고사항이지만 되도록 지킨다.
	- 안 지킬시 equals 메소드와 일관되지 않다 고 명시한다.
```

### 💧CompareTo 메서드 작성 요령

: equals와 비슷한다.
- compareTo 메서드의 인수 타입은 컴파일타임에 정해진다.

  - Comparable은 제네릭 인터페이스이기 때문이다.
  - 인수타입이 잘못되면 컴파일 자체가 되지 않는다.
- compareTo 메서드는 각 필드가 동치인지를 비교하는 게 아니라 그 순서를 비교한다.

  - 객체 참조 필드를 비교하려면 compareTo 메서드를 재귀적으로 호출한다.
  - Comparable을 구현하지 않은 필드나 표준이 아닌 순서로 비교해야 한다면 비교자(Comparator)를 대신 사용한다.
- compareTo에서 관계연산자 `>` `<` 를 사용하는 건 추천하지 않는다.

- 필드가 여러개를 비교할 때 1) 중요한 필드순으로 비교하고 2) 결과값이 나오면 바로 반환한다.

    ```java
    @Override
        public int compareTo(PhoneNumber phoneNumber) {
            int result = Short.compare(areaCode, phoneNumber.areaCode); // 가장 중요한 필드
            if (result == 0) {
                result = Short.compare(prefix, phoneNumber.prefix); // 두 번째로 중요한 필드
                if (result == 0) {
                    result = Short.compare(lineNum, phoneNumber.lineNum); // 세 번째로 중요한 필드
                }
            }
            return result;
        }
    ```

  ### 💧Comparator와 비교자 생성 메소드

  : 자바를 통해 정적 베교자 생성 메소드들을 이름만으로도 사용할 수 있어서 코드가 훨씬 깔끔하다.

    ```java
    public class PhoneNumber implements Comparable<PhoneNumber> {
        private static final Comparator<PhoneNumber> COMPARATOR = comparingInt((PhoneNumber phoneNumber) -> phoneNumber.areaCode)
                .thenComparingInt(phoneNumber -> phoneNumber.prefix)
                .thenComparingInt(phoneNumber -> phoneNumber.lineNum);
    
        private Short areaCode;
        private Short prefix;
        private Short lineNum;
    
        public int compareTo(PhoneNumber phoneNumber) {
            return COMPARATOR.compare(this, phoneNumber);
        }
    }
    ```

  ### 💧 Comparator와 보조 생성 메서드(기본 타입)

  - Comparator는 long과 double용으로는 `comparingInt`와 `thenComparingInt`의 변형 메서드를 갖고 있다.
  - short처럼 더 작은 정수 타입에는 int용 버전을 사용하면 된다. 마찬가지로 float는 double용을 이용해 수행한다.

  ### 💧 Comparator와 보조 생성 메서드(객체 참조 타입)

    ```java
    - 객체 참조용 비교자 생성 메서드도 있다. comparing이라는 정적 메서드 2개가 다중정의되어 있다.
    - 첫 번째는 키 추출자를 받아서 그 키의 자연적 순서를 사용한다.
    - 두 번째는 키 추출자 하나와 추출된 키를 비교할 비교자까지 총 2개의 인수를 받는다.
    - 또한, thenComparing이란 인스턴스 메서드가 3개 다중정의되어 있다.
    - 첫 번째는 비교자 하나만 인수로 받아 그 비교자로 부차 순서를 정한다.
    - 두 번째는 키 추출자를 인수로 받아 그 키의 자연적 순서로 보조 순서를 정한다.
    - 세 번째는 키 추출자 하나와 추출된 키를 비교할 비교자까지 총 2개의 인수를 받는다.
    ```

  ### 💧[권고] 정적 compare 메서드를 활용한 비교자

    ```java
    class HashCodeOrder {
        static Comparator<Object> staticHashCodeOrder = new Comparator<Object>() {
            @Override
            public int compare(Object o1, Object o2) {
                return Integer.compare(o1.hashCode(), o2.hashCode());
            }
        };
    }
    ```

  ### 💧 [권고] 비교자 생성 메서드를 활용한 비교자

    ```java
    class HashCodeOrder {
        static Comparator<Object> comparatorHashCodeOrder = Comparator.comparingInt(o -> o.hashCode());
    }
    ```

