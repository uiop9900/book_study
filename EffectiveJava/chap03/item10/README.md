## 10. equals는 일반규약을 지켜 재정의하라.

: Object가 제공하는 메소드인 `equals`는 최대한 재정의`OverRide`하지 않는다. 하지만 재정의 해야할 일이 생긴다면, 아래의 일반규약들을 다 지키면서 재정의해야한다.

### 🔴 Equals를 재정의하지 않는 경우

: 하나라도 해당되면 재정의하지 않는다.

- 각 인스턴스가 본질적으로 고유하다.
- 논리적 동치성을 검사할 일이 없다.
  - 내부값의 세부적인 비교가 필요없다.
- 상위 클래스에서 재정의한 equals가 하위 클래스에도 맞는다.
- 클래스가 private 혹은 package-private이고, equals를 호출할 일이 없다.

### 🟡 Equals를 재정의하는 경우

- : 논리적 동치성을 확인해야 하는데, 비교하도록 재정의되지 않았을때 이다.

  여기서 논리적 동치성이란? `Integer`, `String`과 같이 값을 표현하는 `값클래스`의 값이 일치하는지 확인하는 것을 말한다.


이때 equals를 정의할때 반드시 일반 규약을 따라야 한다. ( 이 때 a, b는 `null` 이 아닌 객체입니다.)

### 1. 반사성

: 동일객체를 비교하면 당연히 true가 나와야 한다.

- `a.equals(a) == true`
- 너무나 당연하다.

### 2. 대칭성

: 두 객체 (a,b)는 서로가 동일값인지에 대해 똑같이 답해야한다.

- `a.equals(b) == true` → `b.equals(a) == true`

### 3. 추이성

: a가 b와 같고, b가 c와 같다면, a와 c도 같다.

- `a.equals(b) == true` && `b.equals(c) == true`
- `a.equals(c) == true`

→ 이때 클래스를 확장하고 새로운 값을 추가하는 형태라면 equals 규약을 만족시키는 방법은 존재하지 않는다.

→ 상속 대신 컴포지션을 사용하면 우회할수있다? (추후설명예정)

### 4. 일관성

- `a.equals(b)` 를 계속 호출하면 계속 `true` 아니면 `false`이다. → 하나의 값만 가진다.

### 5. null-아님

: 모든 객체는 null 이 아니어야 한다.

```java
// NPE 방지	
@Override
public boolean equals(Object o) {
	if( o == null) {
	return false;
	}
}

// 타입변환하면서 NPE 방지
@Override
public boolean equals(Object o) {

	if(!(o instanceOf MyType)) { // 파라미터가 내가 의도한 타입이 아니면 return 한다.
	return false;
	}

}
```

- `instanceOf`를 사용하게 되면 첫번째 객체가 null인 경우 바로 false를 리턴한다. → npe 로직 작성하지 않아도 된다.
- `a.equals(null) == false`

### 🟢  Equals 구현 방법

- == 로 자기 자신의 참조인지 확인한다.
- instanceOf로 입력이 올바른 타입인지 확인하다.
- 올바른 타입으로 형변환한다.
- 입력된 객체와 핵심 필드들이 모두 일치하는지 하나씩 검사한다.
  - double, float 는 compare로 그 외는 ==로 비교
    - double, float는 특수한 부동소수값 존재
    - compare를 쓰는건, equals 사용시 오토박싱이 될수도 있어서 성능의 문제로.
- null 값이 정상갑승로 취급하고자 한다면, NPE를 방지하기 위해 `Objects.equals(a,b);` 사용한다.