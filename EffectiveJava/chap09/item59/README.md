## 59. 라이브러리를 익히고 사용하라.

### 🎃 괜찮아보이나, 사실 문제가 많은 코드

```java
static Random rnd = new Random();

static int random(int n) {
    return Math.abs(rnd.nextInt()) % n;
}
```

- `n`이 그리 크지 않은 2의 제곱수라면 같은 수열이 반복된다.
- `n` 이 2의 제곱수가 아니라면 몇몇 숫자가 평균적으로 더 자주 반환된다.

### 🎃 보완한 코드 + 문제

```java
public static void main(String[] args) {
    int n = 2 * (Integer.MAX_VALUE / 3);
    int low = 0;
    for(int i = 0; i < 1000000; i++) {
      if(random(n) < n / 2) low++;
    }
    System.out.println(low);
}
```

- `random`메소드는 종종 지정한 범위의 바깥의 수가 튀어나온다.

### 🎃 그렇다면 해결방법은?

: `Random.nextInt()` 를 사용하면 위의 문제들이 다 해결된다.

메소드의 자세한 동작방식은 몰라도, 여러 전문가가 만들어냈기에 안심하고 사용할 수 있다.

**→ 표준 라이브러리를 사용하면 그 코드를 작성한 전문가의 지식과 여러분보다 앞서 사용한 다른 프로그래머들의 경험을 활용할 수 있다.**

- `Random()` 보다는 `ThreadLocalRandom`으로 대체해서 작동시킨다.

### 🎃 라이브러리 사용의 장점은?

- 핵심적인 일과 크게 관련없는 문제로 시간을 허비하지 않아도 된다.
- 따로 노력하지 않아도 성능이 지속해서 개선된다.
- 기능이 점점 더 많아진다.
- 내가 작성한 코드가 남들에게도 낯익은 코드가 된다.
- 메이저 릴리즈마다 주목할만한 수많은 기능들이 라이브러리에 추가된다.
- `java.lang`, `java.util`, `java.io`와 하위패키지에는 익숙해져야 한다.