## 11. equals를 재정의하려거든 hashCode도 재정의하라.

: equals를 재정의하였다면, 해당 클래스에서 hashCode도 재정의 해야한다. 그렇지않으면 `HashMap`, `HashSet` 등 컬렉션의 원소로 사용할때 문제가 생긴다.

### 💡일반 규약

- `equals` 의 정보가 변경되지 않았다면, 해당 애플리케이션이 실행되는 동안, `hashCode`를 여러번 호출해도 동일한 값을 반환해야 한다.
    - 단, 애플리케이션을 재시작하면 값이 달라져도 상관없다.
- `equals`가 두 객체를 같다고 판단했다면, 두 객체의 `hashCode`는 동일값이다.
- `equals`가 두 객체를 다르다고 판단해도, 두 객체의 `hashCode`이 꼭 다를 필요는 없다.

### 🧭 논리적으로 같은 객체는 같은 해시코드를 반환해야한다.

- `equals`는 물리적으로 다른 객체를 논리적으로 같다고 할 수 있다.

    ```java
    @Test
        void javaTest() {
            String a = "100";
            Integer b = 100;
            boolean result = a.equals(b.toString());
            System.out.println("result = " + result); // result = true
        }
    ```

- `hashCode`는 무조건 둘이 다르다고 판단해서 서로 다른 값을 반환한다.

    ```java
    Map<PhoneNumber, String> map = new HashMap<>();
    map.put(new PhoneNumber(010,1234,5678), new Person("리치"));
    ```

  : `map.get(new PhoneNumber(010,1234,5678))`를 하면 put 한 값과 동일하지만 “리치”가 아니라 null 이 나온다.

  → get 할때 PhoneNumber 객체를 새롭게 생성해서 넣었다.

  → hashCode를 재정의 하지않아서 두 객체가 다르다고 판단

  → 못 찾음

  **→ `HashMap`은 해시코드가 서로 다른 엔트리끼리는 동치성 비교를 시도조차 하지 않는다.**


### 🧭 좋은 해시 함수는 서로 다른 인스턴스에 다른 해시코드를 반환한다.

- 전형적인 hashCode 메소드

    ```java
    @Override
        public int hashCode() {
            int result = Integer.hashCode(areaCode);
            result = 31 * result + Integer.hashCode(prefix);
            result = 31 * result + Integer.hashCode(lineNum);
            return result;
        }
    ```

- 성능은 조금 아쉽지만 훨씬 간단한 코드

    ```java
    @Override public int hashCode() {
    	return Objects.hash(lineNum, prefix, areaCode);
    }
    ```

- 클래스가 불변이고 해시코드를 계산하는 비용이 크다면, 캐싱하는 방식을 고려해본다.

    ```java
    private int hashCode; // Automatically initialized to 0
    
    @Override public int hashCode() {
    	int result = hashCode;
    	if (result == 0) {
    		result = Short.hashCode(areaCode);
    		result = 31 * result + Short.hashCode(prefix);
    		result = 31 * result + Short.hashCode(lineNum);
    		hashCode = result;
    	}
    	return result;
    }
    ```


### 🧭 해시코드 생성 시 주의사항

- 해시코드를 계산할 때, 핵심 필드를 생략해서는 안된다.
- hashCode가 반환하는 값의 생성규칙을 사용자에게 자세히 공표하지 않는다.