## 9. try-finally 보다는 try-with-resources를 사용하라.

- 기존에 쓰던 try-finally

    ```java
    try {
    	// 시도하고자 하는 메소드
    } finally {
    	// try와 상관없이 무조건 실행되는 메소드
    }
    ```

- 거기에 try가 하나만 더 추가되어도 복잡하다.

    ```java
    try {// 시도하고자 하는 메소드
    	
    		try {//시도하고자 하는 메소드2
    		} finally {// try와 상관없이 무조건 실행되는 메소드2
    		}
    
    finally {
    	// try와 상관없이 무조건 실행되는 메소드
    }
    ```


try와 finally 둘 다 에러가 발생할 수 있는데 try-finally를 하게 되면 2번째 에러(finally)가 1번째에러(try)를 완전히 먹어버린다. → 디버깅이 어렵다.

### `Try-with-resources`란?
: **try에 자원 객체를 전달하면, try 코드 블록이 끝나면 자동으로 자원을 종료해주는 기능이다.**

이때 자원은 `AutoCloseable`을 구현한 객체로 한정된다. → 그래서 따로 finally에 close()를 하지 않아도 알아서 종료된다.