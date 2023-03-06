---
marp: true
paginate: true
---

# Typescript

- `프로그래밍 언어` : 자바스크립트의 모든 구문과 타입을 정의하고 사용하기 위한 새로운 타입스크립트 고유 구문이 포함된 언어
- `타입 검사기` : 자바스크립트 및 타입스크립트로 작성된 일련의 파일에서 생성된 모든 구성 요소(변수, 함수 등)를 이해하고, 잘못 구성된 부분을 알려주는 프로그램
- `컴파일러` : 타입 검사기를 실행하고 문제를 보고한 후 이에 대응되는 자바스크립트 코드를 생성하는 프로그램
- `언어 서비스` : 타입 검사기를 사용해 비주얼 스튜디오 코드와 같은 편집기에 개발자에게 유용한 유틸리티 제공법을 알려주는 프로그램

> 참고 : 1.3 타입스크립트

---

# 오류 종류

- 구문 오류
  - 타입스크립트가 코드로 이해할 수 없는 잘못된 구문을 감지할 때 발생합니다.
  ```typescript
  // .ts
  let let value;
  // .js
  let let, value;
  ```
- 타입 오류
  - 타입스크립트의 타입 검사기가 프로그램의 타입에서 오류를 감지했을 때 발생합니다.
  ```typescript
  // .ts
  let value: string = 99;
  // .js
  let value = 99;
  ```

---

# Type Annotation

- 초기화 하지 않은 변수의 타입은 any로 간주합니다.
- 이를 방지하고자 Type Annotation을 작성합니다.

```typescript
let value: number;

value = 99; // OK
value = "99"; // ERROR
```

---

# Module

- 모듈 : export 또는 import가 있는 파일
- 스크립트 : 모듈이 아닌 모든 파일
- 타입스크립트는 스크립트로 작성된 코드는 전역 스코프로 간주합니다.

```typescript
// a.ts
let value = "A"; // ERROR : Cannot redeclare block-scoped variable 'value'.

// b.ts
let value = "B"; // ERROR : Cannot redeclare block-scoped variable 'value'.
```
