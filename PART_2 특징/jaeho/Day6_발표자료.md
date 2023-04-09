---
marp: true
paginate: true
---

# 인터페이스와 추상 클래스

---

# 인터페이스 예시 코드

```tsx
interface InterfaceTest1 {
  property1: string;
  method1(): number;
}

class Test1 implements InterfaceTest1 {
  // ERROR : Type 'Test1' is missing the following properties
  //          from type 'InterfaceTest1': property1, method1
}

const test1 = new Test1();
console.log(test1.property1);
// ERROR : Property 'property1' does not exist on type 'Test1'.
```

---

# 추상 클래스 예시 코드

```tsx
abstract class AbstractTest2 {
  abstract property2: string;
  abstract method2(): number;
}

class Test2 extends AbstractTest2 {
  // ERROR : Non-abstract class 'Test2' does not implement
  //          inherited abstract member 'method2' from class 'AbstractTest2'.
  // ERROR : Non-abstract class 'Test2' does not implement
  //          inherited abstract member 'property2' from class 'AbstractTest2'.
}

const test2 = new Test2();
console.log(test2.property2);
```

---

## 둘 다 클래스의 구현을 강제하는데 중복되는 기능이 아닌가?

- interface 활용이 더 범용적이니 abstract 쓸 일이 있을까?

---

# 의미와 목적의 차이

> 타입 별칭과 인터페이스처럼?

- 인터페이스
  - 구현 대상의 명세 (implements 키워드)
- 추상 클래스
  - 상속 관계를 나타내기 위함 (extends 키워드)
  - abstract 키워드를 추가하지 않은 공통 멤버를 작성할 수 있다.

---

# 참고

- [Class Heritage (공식문서)](https://www.typescriptlang.org/docs/handbook/2/classes.html#class-heritage)
- [What is the difference between interface and abstract class in Typescript? (StackOverflow)](https://stackoverflow.com/questions/50110844/what-is-the-difference-between-interface-and-abstract-class-in-typescript)

---

## 그 외 궁금한 부분

- p153
  - 생성자 내의 할당에 대해서 그 멤버가 클래스에 존재하는 멤버인지 추론하려고 시도하지 않습니다. 하면서 예시코드는 존재하는지 검사하지 않나요?
