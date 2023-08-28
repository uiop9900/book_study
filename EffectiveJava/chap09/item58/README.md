## 58. 전통적인 for문보다는 for-each문을 사용하라.

```java
for(int i = 0; i < a.length; i++) {
    int z = a[i]; // a[i]로 무언가를 한다.
}
```

: 코드가 지저분해지고 변수가 많아져 오류가 생길 위험이 있다.

### 🎃 for-each문으로 해결한다.

: enhanced for statement

- 반복자와 인덱스 변수를 사용하지 않으니 코드가 깔끔해지고 오류가 날 일이 없다.

- 하나의 관용구로 되어있어서 배열이든 컬렉션이든 코드의 형태가 같다.

- 콜론(:)은 **안의(in)** 라고 읽으면 된다. (elements안의 각 원소 e에 대해)

- 버그 예시(1)

    ```java
    enum Suit { CLUB, DIAMOND, HEART, SPADE}
    enum Rank { ACE, DEUCE, THREE, FOUR, FIVE, SIX, SEVEN, EIGHT, NINE, TEN, JACK, QUEEN, KING}
    
    static Collection<Suit> suits = Arrays.asList(Suit.values());
    static Collection<Rank> ranks = Arrays.asList(Rank.values());
    
    List<Card> deck = new ArrayList<>();
    for(Iterator<Suit> i = suits.iterator(); i.hasNext();) {
        for(Iterator<Rank> j = ranks.iterator(); j.hasNext();) {
            deck.add(new Card(i.next(), j.next())); // 여기서 버그
        }
    }
    ```

  : `i`는 `Suit`를 기준으로 호출되어야 하는데, 마지막의 `i.next()`는 안쪽에서 호출되기 때문에 `Rank`를 기준으로 호출된다. → 숫자가 바닥나면 `NoSuchElementException` 가 터진다.

- 버그 예시(2)

    ```java
    enum Suit { CLUB, DIAMOND, HEART, SPADE}
    enum Rank { ACE, DEUCE, THREE, FOUR, FIVE, SIX, SEVEN, EIGHT, NINE, TEN, JACK, QUEEN, KING}
    
    static Collection<Suit> suits = Arrays.asList(Suit.values());
    static Collection<Rank> ranks = Arrays.asList(Rank.values());
    
    List<Card> deck = new ArrayList<>();
    for(Suit suit : suits) {
        for(Rank rank : ranks) {
            deck.add(new Card(suit, rank));   
        }
    }
    ```

  : 예상한것은 6 X 6 = 36개의 값이 출력되는 것이지만, 실제로는 `ONE ONE` ~ `SIX SIX` 의 6쌍만 출력하고 끝난다.

- forEach를 이용해서 보완하기

    ```java
    for (Suit suit : suits) 
    	for (Rank rank : ranks) 
    		deck.add(new Card(suit, rank));
    ```


### 🎃 for-each문을 사용할 수 없는 상황

- 파괴적인 필터링
  - 컬렉션을 순회하면서 원소를 제거하는 경우
- 변형
  - 컬렉션을 순회하면서 교체해야하는 경우
- 병렬반복
  - 병렬로 순회하는 경우