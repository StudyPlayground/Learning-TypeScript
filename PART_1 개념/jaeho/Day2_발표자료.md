---
marp: true
paginate: true
---

# 목차

- Narrowing
- Narrowing의 종류
- 참고자료

---

# Narrowing

> 타입 좁히기   

- `typeof` type guards
- Truthiness narrowing
- Equality narrowing
- The `in` operator narrowing
- `instanceof` narrowing
- Assignments
- Control flow analysis
- Using type predicates
- Discriminated unions
- The `never` type
- Exhaustiveness checking

---

# `typeof` type guards

- `typeof` 연산자는 Javascript에서 피연산자의 평가 전 자료형을 나타내는 문자열을 반환합니다.
- "string", "number", "bigint", "boolean", "symbol", "undefined", "object", "function"

---

## `typeof` type guards 주의점

```tsx
function printAll(strs: string | string[] | null) {
  if (typeof strs === "object") {
    for (const s of strs) {
      console.log(s);  // ERROR : Object is possibly 'null'.
    }
  } else {
    // ...
  }
}
```

---

## `typeof` type guards 주의점

```tsx
interface A { swim: string; }
interface B { age: number; }

const a = { swim: "swimming" };
const b = { age: 20 };

function isEqual (param1: A | B, param2: A | B) {
  if (typeof param1 === typeof param2) {
    console.log("Equal");
  } else {
    console.log("Uneqaul");
  }
}

isEqual(a, a);
isEqual(a, b);
```

---

# Truthiness narrowing

```tsx
function getUsersOnlineMessage(numUsersOnline: number) {
  if (numUsersOnline) {
    return `There are ${numUsersOnline} online now!`;
  }

  return "Nobody's here. :(";
}
```

---

## Truthiness narrowing 주의점

- Falsy 값 종류 : 0, NaN, "", null, undefined, 0n (the bigint version of zero)

```tsx
let value = Math.random() > 0.5 && "over half";

if (value) {
  console.log(value); // type is string
} else {
  console.log(value); // type is string | false
}
```

---

# Equality narrowing

```tsx
function example(x: string | number, y: string | boolean) {
  if (x === y) {
    x.toUpperCase();  // (method) String.toUpperCase(): string
    y.toLowerCase();  // (method) String.toUpperCase(): string
  } else {
    console.log(x);  // (parameter) x: string | number
    console.log(y);  // (parameter) y: string | number
  }
}
```

---

# The `in` operator narrowing

- `in` 연산자는 해당 속성이 해당 객체에 존재하는지에 따라 boolean을 반환합니다.

```tsx
type Fish = { swim: () => void };
type Bird = { fly: () => void };

function move(animal: Fish | Bird) {
  if ("swim" in animal) {
    return animal.swim();
  }

  return animal.fly();
}
```

---

# `instanceof` narrowing

- instanceof 연산자는 생성자의 prototype 속성이 객체의 프로토타입 체인 어딘가 존재하는지 판별합니다.

```tsx
function logValue(x: Date | string) {
  if (x instanceof Date) {
    console.log(x.toUTCString());  // (parameter) x: Date
  } else {
    console.log(x.toUpperCase());  // (parameter) x: string
  }
}
```

---

# Assignments

- 할당

```tsx
let x = Math.random() < 0.5 ? 10 : "hello world!";  // let x: string | number

x = 1;  // let x: number

x = "goodbye!";  // let x: string

x = true;  // ERROR : Type 'boolean' is not assignable to type 'string | number'.
```

<!-- let으로 선언하였기 때문에 "hello world"의 타입이 string으로 추론됩니다. -->

---

# Control flow analysis

- if문, while문 등의 조건문

```tsx
function padLeft(padding: number | string, input: string) {
  if (typeof padding === "number") {
    return " ".repeat(padding) + input;  // padding: number
  }

  return padding + input;  // padding: string
}
```

---

# Using type predicates

- 반환값은 boolean
- 매개변수의 타입을 정한다.

```tsx
type Fish = { swim: () => void };
type Bird = { fly: () => void };

declare function getSmallPet(): Fish | Bird;

function isFish(pet: Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined;
}

let pet = getSmallPet();

if (isFish(pet)) {
  pet.swim();  // let pet: Fish
} else {
  pet.fly();  // let pet: Bird
}
```

<!-- 함수의 참, 거짓 여부에 따라 해당 매개변수의 타입이 무엇일지 결정해줍니다. -->

---

# Discriminated unions

```tsx
interface Shape {
  kind: "circle" | "square";
  radius?: number;
  sideLength?: number;
}

function handleShape(shape: Shape) {
  if (shape.kind === "rect") {
    // This condition will always return 'false'
  }
  
  if (shape.kind === "circle") {
    return Math.PI * shape.radius ** 2;  // Object is possibly 'undefined'.
  }

  return Math.PI * shape.radius! ** 2;  // non-null assertions
}
```

---

## Discriminated unions 코드

```tsx
interface Circle {
  kind: "circle";
  radius: number;
}

interface Square {
  kind: "square";
  sideLength: number;
}

type Shape = Circle | Square;

function getArea(shape: Shape) {
  switch (shape.kind) {
  case "circle":
    return Math.PI * shape.radius ** 2;  // (parameter) shape: Circle
  case "square":
    return shape.sideLength ** 2;  // (parameter) shape: Square
  }
```

---

# The `never` type

```tsx
function test (param: string | number) {
  if (typeof param === "string") {
    // ..
  } else if (typeof param === "number") {
    // ..
  } else {
    param  // param: never
  };
}
```

---

# Exhaustiveness checking

```tsx
interface Circle {
  kind: "circle";
  radius: number;
}

interface Square {
  kind: "square";
  sideLength: number;
}

interface Triangle {
  kind: "triangle";
  sideLength: number;
}

type Shape = Circle | Square | Triangle;

function getArea(shape: Shape) {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.sideLength ** 2;
    default:
      const _exhaustiveCheck: never = shape; // ERROR : Type 'Triangle' is not assignable to type 'never'.
      return _exhaustiveCheck;
  }
}
```

<!-- never는 어느 타입의 변수에든 할당할 수 있습니다. -->
<!-- 하지만 never는 어느 타입의 변수로부터 할당 받을 수 없습니다. (never 자신 제외) -->

---

# 참고자료

- [Typescript 공식문서 - Narrowing](https://www.typescriptlang.org/docs/handbook/2/narrowing.html)