## 73. 추상화 수준에 맞는 예외를 던져라.

### 😵‍💫 예외 번역

- 수행하려는 일과 관련없어 보이는 예외가 나오면 당황스럽다.

  : 상위 계층에서는 저수준 예외를 잡아 자신의 추상화 수준에 맞는 예외로 바꿔 던져야 한다.

```java
try{
	...
} catch(LowerLevelException e){
	// 추상화 수준에 맞춘다
	throw new HigherLevelException(...);
}
```

### 😵‍💫 예외 연쇄

: 근본 원인인 저수준 예외를 고수준 예외로 실어서 보내는 방식이다.

- 별도의 접근자 메소드로 저수준 예외를 꺼내서 확인할 수 있다.

```java
try{
	... // 저수준 추상화를 이용한다.
}catch(LowerLevelException cause){
	// 저수준 예외를 고수준 예외에 실어 보낸다.
    throw new HigherLevelException(cause);
}
```

- 최종적으로 `Throwable` 생성자까지 건네줄수 있다.

```java
class HigherLevelException extends Exception{
	HigherLevelException(Throwable cause){
    	super(cause);
    }
}
```

→ 무턱대로 예외를 전파하는 것보다는 예외 번역에 좋지만, 남용해서는 안된다.

→ 아래 계층에서 최대한 예외를 발생시키지 않는 것이 최선이다.

→ 로깅 기능을 활용하는 것도 좋다.