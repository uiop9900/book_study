## 52. 다중정의는 신중히 사용하라.

### 🌳 다중정의란?

```java
public class CollectionClassifier {
		// 다중정의1 
    public static String classify(Set<?> s) {
        return "집합";
    }
		// 다중정의2
    public static String classify(List<?> lst) {
        return "리스트";
    }
		// 다중정의3
    public static String classify(Collection<?> c) {
        return "그 외";
    }

    public static void main(String[] args) {
        Collection<?>[] collections = {
                new HashSet<String>(),
                new ArrayList<BigInteger>(),
                new HashMap<String, String>().values()
        };

        for (Collection<?> c : collections)
            System.out.println(classify(c));
    }
}
```

: `그 외` 가 3번 출력된다.

- `classify` 메소드는 다중정의되어서 컴파일 시점에 어떤 `classify` 가 불려질지 결정된다.
- 그래서 for 문의 타입은 `Collection<?>` 이다.
- **재정의한 메소드는 동적으로 선택되고 다중정의한 메소드는 정적으로 선택된다.**

→ 의도대로 동작하게 하려면, `instanceOf`를 사용해서 출력한다.

```java
public static String classify(Collection<?> c) {
    return c instanceof Set  ? "집합" :
            c instanceof List ? "리스트" : "그 외";
}
```

### 🌳 재정의(Override)

```java
class Wine {
    String name() { return "포도주"; }
}

class SparklingWine extends Wine {
    @Override String name() { return "발포성 포도주"; }
}

class Champagne extends SparklingWine {
    @Override String name() { return "샴페인"; }
}

public class Overriding {
    public static void main(String[] args) {
        List<Wine> wineList = List.of(
                new Wine(), new SparklingWine(), new Champagne());

        for (Wine wine : wineList)
            System.out.println(wine.name());
    }
}
```

: `Wine` 을 재정의했다.

- 실행하면 예상대로 "포도주", "발포성 포도주", "샴페인"을 차례대로 출력된다.
- 즉, 가장 하위에서 정의한 재정의 메소드가 실행된다.

→ 다중정의는 컴파일타임에 선택되며, 오직 매개변수의 컴파일타임 타입에 의해 결정된다.

### 🌳 다중정의가 혼동을 일으키는 상황을 피해야 한다.

- 매개변수 수가 같은 다중정의는 만들지 않는다.
- 다중정의하는 대신 메소드 이름을 다르게 지어주는 방법도 있다.
- 생성자는 재정의 불가하기때문에 다중정의와 재정의가 혼용될 수 없다.
- 매개변수가 근본적으로 다르면 헷갈릴 수 없다.
    - 근본적으로 다르다 == 형변환이 불가하다.

### 🌳 형변환이 가능해져서 나오는 다중정의

```java
public class SetList {
    public static void main(String[] args) {
        Set<Integer> set = new TreeSet<>();
				List<Integer> list = new ArrayList<>();

        for (int i = -3; i < 3; i++) {
            set.add(i);
						list.add(i);
        }

        for (int i = 0; i < 3; i++) {
            set.remove(i);
						list.remove(i); // 다중정의 되어있다.
        }
        System.out.println(set + " " + list);
    }

}
```

- 예상값
    - [-3, -2, -1] [-3, -2, -1]
- 실제값
    - [-3, -2, -1] [2, 0, 2]

→ list의 값이 이상하다?

- `list.remove(i);` 가 다중정의 되어있어서 매개변수가 `Integer/Object`에 따라 동작이 다르게 한다.
- 기존에는 `Obejct`와 `int`가 근본적으로 달라서 영향받지 않았지만, 자바4 이후부터 서로 간 형변환이 가능해져서 의도대로 사용하려면 아래와 같이 사용해야한다.

```java
// 생략
   for (int i = 0; i < 3; i++) {
            set.remove(i);
						list.remove((Integer)i); 
        }
        System.out.println(set + " " + list);
```

### 🌳 정리

- 일반적으로 매개변수 수가 같을 때는 다중정의를 피하는 게 좋다.
- 만약 다중정의를 피할 수 없는 상황이라면 헷갈릴 만한 매개변수는 형변환하여 정확한 다중정의 메서드가 선택되도록 하자.