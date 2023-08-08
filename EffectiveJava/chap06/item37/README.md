## 37. ordinal 인덱싱 대신 EnumMap을 사용하라.

- 예시 클래스

    ```java
    public class Plant {
        final String name;
        final LifeCycle lifeCycle;
    
        public Plant(String name, LifeCycle lifeCycle) {
            this.name = name;
            this.lifeCycle = lifeCycle;
        }
    
        @Override
        public String toString() {
            return name;
        }
    }
    
    public enum LifeCycle {
        ANNUAL, PERNNIAL, BIENNIAL
    }
    ```


### 🕹️ 열거타입의 인덱싱을 ordinal로 하는 경우

```java
public static void usingOrdinalArray(List<Plant> garden) {
        Set<Plant>[] plantsByLifeCycle = (Set<Plant>[]) new Set[LifeCycle.values().length];
        for (int i = 0 ; i < plantsByLifeCycle.length ; i++) {
            plantsByLifeCycle[i] = new HashSet<>();
        }

        for (Plant plant : garden) {
            plantsByLifeCycle[plant.lifeCycle.ordinal()].add(plant); // ordinal 사용
        }
				
				// 결과 출력
        for (int i = 0 ; i < plantsByLifeCycle.length ; i++) {
            System.out.printf("%s : %s%n",
                    LifeCycle.values()[i], plantsByLifeCycle[i]);
        }
    }
```

### 🕹️ EnumMap을 사용하기

: 열거타입을 키로 사용하는 Map이다.

```java
public static void usingEnumMap(List<Plant> garden) {
				// 기존 Set -> Map으로 변경
        Map<LifeCycle, Set<Plant>> plantsByLifeCycle = new EnumMap<>(LifeCycle.class);

        for (LifeCycle lifeCycle : LifeCycle.values()) {
						// enum 값을 키로 Map을 만든다.
            plantsByLifeCycle.put(lifeCycle,new HashSet<>());
        }

        for (Plant plant : garden) {
            plantsByLifeCycle.get(plant.lifeCycle).add(plant);
        }

        //EnumMap은 toString을 재정의하였다.
        System.out.println(plantsByLifeCycle);
    }
```

- 내부에서 배열을 사용하기 때문에 안정성과 배열의 성능을 둘 다 챙겼다.

### 🕹️ Stream으로 구현해보기

```java
public static void streamV1(List<Plant> garden) {
        Map plantsByLifeCycle = garden.stream().collect(Collectors.groupingBy(plant -> plant.lifeCycle));
        System.out.println(plantsByLifeCycle);
    }

    public static void streamV2(List<Plant> garden) {
        Map plantsByLifeCycle = garden.stream()
                .collect(Collectors.groupingBy(plant -> plant.lifeCycle,
                        () -> new EnumMap<>(LifeCycle.class),Collectors.toSet()));
        System.out.println(plantsByLifeCycle);
    }
```

- `streamV1` 메소드
  - `EnumMap`이 아닌 `Map`을 구현한거라, `EnumMap`의 장점이 사라진다.
- `streamV2` 메소드
  - 매개변수 3개짜리인 `groupingBy` 를 이용해서 EnumMap을 구현했다.

### 🕹️ EnumMap과 Stream으로 구현한 EnumMap의 차이

- EnumMap 버전
  - 언제나 하나씩의 중첩맵을 만든다.
  - 예) 하루살이와 여러해살이 식문만 살고, 두해살이가 없다면 → 3개가 만들어진다.
- 스트림 버전
  - 있는 경우에만 생성한다.
  - 예) 하루살이와 여러해살이 식문만 살고, 두해살이가 없다면 → 2개가 만들어진다.

### 🕹️ 복잡한 예시

- 기존

    ```java
    public enum Phase {
        SOLID, LIQUID, GAS;
    
        public enum Transition {
            MELT,FREEZE, BOIL, CONDENSE, SUBLIME, DEPOSIT;
    
            private static final Transition[][] TRANSITIONS = {
                    {null, MELT, SUBLIME},
                    {FREEZE, null, BOIL},
                    {DEPOSIT, CONDENSE, null}
            };
    
            public static Transition from(Phase from, Phase to) {
                return TRANSITIONS[from.ordinal()][to.ordinal()]; // ordinal 사용
            }
        }
    }
    ```

  - 컴파일러는 ordinal과 배열 인덱스의 관계를 알 수가 없다. → 예외 혹은 이상한 값을 가지게 된다.
- 보완

    ```java
    public enum Phase {
        SOLID, LIQUID, GAS;
    
        public enum Transition {
            MELT(SOLID, LIQUID),
            FREEZE(LIQUID, SOLID),
            BOIL(LIQUID, GAS),
            CONDENSE(GAS, LIQUID),
            SUBLIME(SOLID, GAS),
            DEPOSIT(GAS, SOLID); // 새로운 게 추가되어도 손쉽게 추가 가능하다.
    
            private final Phase from;
            private final Phase to;
    
            Transition(Phase from, Phase to) {
                this.from = from;
                this.to = to;
            }
    
            private static final Map<Phase, Map<Phase, Transition>> transitionMap;
    
            static {
                transitionMap = Stream.of(values())
                        .collect(Collectors.groupingBy(t -> t.from, // 바깥 Map의 Key
                                () -> new EnumMap<>(Phase.class), // 바깥 Map의 구현체
                                Collectors.toMap(t -> t.to, // 바깥 Map의 Value(Map으로), 안쪽 Map의 Key
                                        t -> t, // 안쪽 Map의 Value
                                        (x,y) -> y, // 만약 Key값이 같은게 있으면 기존것을 사용할지 새로운 것을 사용할지
                                        () -> new EnumMap<>(Phase.class)))); // 안쪽 Map의 구현체
            }
    
            public static Transition from(Phase from, Phase to) {
                return transitionMap.get(from).get(to);
            }
        }
    }
    ```


→ 배열의 인덱스를 얻기위해서는 ordinal이 아닌 최대한 EnumMap을 사용한다.