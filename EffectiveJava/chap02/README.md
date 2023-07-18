
# 02. 객체 생성과 파괴

## 학습목표

- 객체를 만들어야 할때와 만들지 말아야할때
- 올바르게 객체를 생성하고 불필요한 생성 피하기
- 제때 객체를 파괴하고 파괴 전에 수행해야하는 정리작업

---

아이템 1. 생성자 대신 정적 팩토리 메소드를 고려하라. <br>
아이템 2. 생성자에 매개변수가 많다면 빌더를 고려하라.<br>
아이템 3. private 생성자나 열거 타입으로 싱글턴임을 보증하라.<br>
아이템 4. 인스턴스화를 막으려거든 private 생성자를 사용하라. <br>
아이템 5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라.<br>
아이템 6. 불필요한 객체 생성을 피하라.<br>
아이템 7. 다 쓴 객체 참조를 해제하라.<br>
아이템 8.  finalizer와 cleaner 사용을 피하라.<br>
아이템 9. try-finally 보다는 try-with-resources를 사용하라.