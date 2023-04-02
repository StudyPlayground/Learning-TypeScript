---
marp: true
paginate: true
---

# 인터페이스와 타입 별칭의 에러 메시지 차이

> 인터페이스는 이름 없는 객체 리터럴의 별칭이 아닌 이름이 있는 객체로 간주되므로 어려운 특이 케이스에서 나타나는 오류 메시지를 좀 더 쉽게 읽을 수 있습니다. p133

---

# 인터페이스의 에러 메시지

- [코드 예시 (Typescript Playground)](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAkgZzARgJLIN4BQyyIcAthAFzIZSgDmyAPiQK5kBG0A3EcnPSptOPAgF8CBUJFiIU6MACZcEAB6QQAEzSoIGHPj6kK1EOy5RexAFYB7DtQBEAMSi3wj5skcAhRAGtHcUkEdwxdDGxqBWVkAF58MV4CMABPAAd5PSwAFXjDYmMhWgYvMxFLPgEhcoteJMk0zIilPITCQvIa81EbeydXdzBPFl8AoLFkADIWzBzk0JBwhRzo7MU2xO4gA)

```tsx
interface test1I {
  name: string | number;
  age: number;
}

interface test2I extends test1I {
  name: number;
  job: "Front" | "Back"
}

const testI: test2I = {};
// ERROR : Type '{}' is missing the following properties from type 'test2I': name, job, age(2739)
```

---

# 타입 별칭의 에러 메시지

- [코드 예시 (Typescript Playground)](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAkgZzARgJLIN4BQyyIcAthAFzIZSgDmyAPiQK5kBG0A3EcnPSptOPAgF8CBUJFiIU6MACZcEAB6QQAEzSoIGHPj6kK1EOy5RexAFYB7DtQBEAMSi3wj5skcAhRAGtHcUkEdwxdDGxqBWVkAF58MV4CMABPAAd5PSwAFXjDYmMhWgYvMxFLPgEhcoteJMk0zIilPITCQvIa81EbeydXdzBPFl8AoLFkADIWzBzk0JBwhRzo7MU2xO4gA)

```tsx
type test1T = {
  name: string | number;
  age: number;
};

type test2T = {
  name: number;
  job: "Front" | "Back"
} & test1T;

const testT: test2T = {};
// ERROR : Type '{}' is not assignable to type 'test2T'.
//         Type '{}' is missing the following properties
//         from type '{ name: number; job: "Front" | "Back"; }': name, job(2322)
```

---

> 다음은 궁금한 부분의 원인을 찾지 못한 부분입니다.

---

# 객체의 키 타입은 오직 string?

> 자바스크립트가 암묵적으로 객체 속성 조회 키를 문자열로 변환하지만 때로는 객체의 키로 숫자만 허용하는 것이 바람직할 수 있습니다. 타입스크립트 인덱스 시그니처는 키로 string 대신 number 타입을 사용할 수 있지만, 명명된 속성은 그 타입을 포괄적인 용도의 string 인덱스 시그니처의 타입으로 할당할 수 있어야 합니다. p142

---

# 그래도 string으로 잡히는데?

- [코드 예시 (Typescript Playground)](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAgCoQM5mQbwFDKHIDaA1hAJ4BcyIArgLYBG0AujVlKAOYDcBRMpQ5guIbu2Sce-AL548AGwjZ6DZAF5kATgAcAdn54EAexBZkkLDXQWt+IsgCMAJgDMNAOSu3ngDQChAD0Qc7ulhQADijAGFKiPAqOal5q-oHIIbSMEdHIsfFi3HiyQA)

```tsx
interface Test {
  [key: number]: string;
  [key: string]: string;
}

let num = 987;

const test: Test = {
  123: "123",
  // 123 type is string

  num: "num",
  // num type is string
};
```

---

# string 인덱스 시그니처의 타입 작성 없어도 되는데..?

- [코드 예시 (Typescript Playground)](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAgCoQM5mQbwFDKHIDaA1hAJ4BcyIArgLYBG0AujVlKAOYDceAXzx4ANhGz0GyALzIAnAA4A7PzwIA9iCzJIWGum2z8RZAEYATAGYaAcguWbAGgJEA9K7NWdFAA4pgGMicPIJAA)

```tsx
interface Test {
  [key: number]: string;
}

let num = 987;

const test: Test = {
  123: "123",
  // 123 type is string
};
```

---

# (번외) 또 이해가 필요한 부분

- [The keyof type operator (공식문서)](https://www.typescriptlang.org/docs/handbook/2/keyof-types.html#the-keyof-type-operator)

```tsx
type Arrayish = { [n: number]: unknown };
type A = keyof Arrayish;
// type A = number

type Mapish = { [k: string]: boolean };
type M = keyof Mapish;
// type M = string | number
```
