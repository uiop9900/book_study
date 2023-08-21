## 45. 스트림은 주의해서 사용하라.

- 스트림(stream)
  - 데이터 원소의 유한 혹은 무한 시퀀스
- 스트림 파이프라인(stream pipeline)
  - 이 원소들로 수행하는 연산 단계를 표현
- 원소 예시
  - 컬렉션, 배열, 파일, 정규표현식 패턴 매처.. 등 어디로부터든 올 수 있다.

### 💬 **스트림 파이프라인 연산**

- **중간 연산(Intermediate Operation)**
  - 각 중간 연산은 스트림을 어떠한 방식으로 `변환하는 역할`을 수행한다.
  - 모두 한 스트림을 다른 스트림으로 변환하게 하여 메서드 체이닝을 가능하게 한다.
  - 예를 들어, 각 원소에 함수를 적용하거나 특정 조건을 만족 못하는 원소를 걸러낼 수 있다.
    - `filter()`, `map()`, `sorted()`
- **종단 연산(Terminal Operation)**
  - 종단 연산은 마지막 중간 연산이 내놓은 스트림에 최후의 `연산을 가하는 역할`을 한다.
  - 예를 들어, 원소를 정렬해 컬렉션에 담거나 특정 원소를 하나 선택하는 식이다.
    - `forEach()`, `collect()`, `match()`, `count()`, `reduce()`

### 💬 **스트림은 지연평가된다.**

: 평가는 `종단 연산`이 호출될때 이뤄지며, `종단 연산`에 쓰이지 않는 데이터 원소는 계산에 쓰이지 않는다.

→ 종단 연산이 없으면 아무런 일도 이뤄지지 않는다. → 빼먹지 말자.

### 💬 스트림 API

메소드 연쇄를 지원하는 플루언트 API이다.

파이프라인 하나를 구성하는 모든 호출을 연결하여 단 하나의 표현식으로 완성할 수 있다.

기본적으로 순차적으로 수행된다.

병렬로 실행하려면 `parallel` 메소드 사용한다.

스트림을 제대로 사용하면 프로그램이 짧고 깔끔해지지만, 잘못 사용하면 읽기 어렵고 유지보수가 어렵다.

- 스트림을 과하게 사용한 경우

    ```java
    public static void main(String[] args) throws IOException {
        File dectionary = new File(args[0]);
        int minGroupSize = Integer.parseInt(args[1]);
    
        try(Stream<String> words = Files.lines(dectionary.toPath())) {
            words.collect(
                groupingBy(word -> word.chars().sorted()
                           .collect(StringBuilder::new,
                                    (sb, c) -> sb.append((char) c),
                                    StringBuilder::append).toString()))
                .values().stream()
                .filter(group -> group.size() >= minGroupSize)
                .map(group -> group.size() + ": " + group)
                .forEach(System.out::println);
        }
    }
    ```

  - 읽기가 어렵다. → 유지보수 힘들다.
- 스트림을 적절히 사용한 경우

    ```java
    public static void main(String[] args) throws IOException {
        File dectionary = new File(args[0]);
        int minGroupSize = Integer.parseInt(args[1]);
    
        try(Stream<String> words = Files.lines(dectionary.toPath())) {
            words.collect(
                groupingBy(word -> word.chars().sorted()
                           .collect(StringBuilder::new,
                                    (sb, c) -> sb.append((char) c),
                                    StringBuilder::append).toString()))
                .values().stream()
                .filter(group -> group.size() >= minGroupSize)
                .map(group -> group.size() + ": " + group)
                .forEach(System.out::println);
        }
    }
    ```

  - 람다에서는 타입 이름을 생략하므로 매개변수 이름을 잘 지어야 가독성이 좋다.

### 💬 스트림을 사용할 때,

- char 값들을 처리할 때는 스트림을 삼가는 편이 좋다.
- 기존 코드는 스트림을 사용하도록 리팩토링하되, 새 코드가 나아보일때만 반영한다.
- 스트림으로 변경하기 좋은 것들
  - 원소들의 시퀀스를 일관되게 변환한다.
  - 원소들의 시퀀스를 필터링한다.
  - 원소들의 시퀀스를 하나의 연산을 사용해 결합한다. (더하기, 연결하기, 최소값 등..)
  - 원소들의 시퀀스를 컬렉션에 모은다
  - 원소들의 시퀀스에서 특정 조건을 만족하는 원소를 찾는다.
- 스트림이 적합하지 않는 경우
  - 데이터가 파이프라인의 여러 단계(stage)를 통과할 때 이 데이터의 각 단계에서의 값들에 동시에 접근하기 어려운 경우
  - 스트림 파이프라인은 한 값을 다른 값에 매핑하고 나면 원래의 값을 잃는 구조이기 때문