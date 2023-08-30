## 63. 문자열은 연결은 느리니 주의하라.

: 문자열 연결 연산자(+)는 여러 문자열을 하나로 합쳐준다.

### 🎃 문자열 연결 연산자로 문자열 n개를 잇는 시간은 **n^2**에 비례한다.

```java
public String statement() {
    String result = "";
    for (int i = 0; i < numItems(); i++) {
        result += lineForItem(i); //문자열 연결
    }
    return result;
}
```

- `String` 대신 `StringBuilder`를 사용한다.

```java
public String statement2() {
    StringBuilder sb = new StringBuilder(numItems() * LINE_WIDTH);
    for (int i = 0; i < numItems(); i++) {
       sb.append(lineForItem(i));
    }
    return sb.toString();
}
```