## 28. 배열보다는 리스트를 사용하라.

### 💡 배열은 공변하지만 제네릭은 불공변이다.

- 공변: 같이 변한다.
  - 배열
    - `Sub`이 `Super`의 하위타입이라면,
    - `List<Sub>`도 `List<Super>`의 하위타입이다.
  - 제네릭
    - `Type1`이 `Type2`의 하위타입이라도
    - `List<Type1>` 과 `List<Type2>` 이 상관없다.
  - 배열은 실수를 런타임이 되어야 알 수 있고, 리스트는 컴파일할때 바로 알 수 있다.

### 💡 배열은 실체화된다.

- 배열: `Object[]`
- 리스트: `List<Object>`
- 배열은 런타임에도 원소의 타입을 인지하고 확인하지만 제네릭은 컴파일타임에만 검사하고 런타임시점에는 소거 된다.
  - → 배열과 제네릭은 잘 어우러지지 않는다.
- 배열은 제네릭타입, 매개변수화타입, 타입 매개변수를 사용할 수 없다.
  - `new List<E>[]`, `new List<String>[]`, `new E[]` 로 작성할 수 없다.
  - 제네릭타입이 안전하지 않기때문에 허용하지 않는다.
  - `ClassCastException`이 발생한다.