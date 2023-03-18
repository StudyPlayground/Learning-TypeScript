---
marp: true
paginate: true
---

# 콜백 함수 타입

## 기본적인 함수 타입 작성

```tsx
function test(message: string) {
  console.log(`You : ${message}`);
}

test("hello");
test(99); // ERROR : Argument of type 'number' is not assignable to parameter of type 'string'.
test(); // ERROR : An argument for 'message' was not provided.
```

---

# 콜백 함수 타입

## 왜 콜백에서는 적은 인수 작성에 에러가 없는 걸까?

```tsx
const arr = [1, 3, 5, 7, 9];

arr.forEach((value) => console.log(value));

// forEach(callbackfn: (value: T, index: number, array: T[]) => void, thisArg?: any): void;
```

---

# 콜백 함수 타입

## 함수 오버로드?

```tsx
function test(message: string): void;
// ERROR : This overload signature is not compatible with its implementation signature.

function test(message: string, index: number): void;
// ERROR : This overload signature is not compatible with its implementation signature.

function test(message: string, index: number, flag: boolean): void;

function test(message: string, index: number, flag: boolean) {
  console.log(`You : ${message}`);
}

test("hello");
test("hello", 1);
test("hello", 1, true);
```

---

# 콜백 함수 타입

## 내부 구현된 것이라 그런가?

```tsx
class Shirt {
  color = "RED";
  id = 0;
  total: string[] = [];
  walk(cb: (value: string, index: number, array: string[]) => void) {
    cb(this.color, this.id, this.total);
    this.id += 1;
    this.total.push(this.color);
    this.color = this.color === "RED" ? "BLUE" : "RED";
  }
}

const human = new Shirt();
function walking(value: string, id: number) {
  console.log(value, id);
}

human.walk(walking);
```

---

## 콜백 함수 타입

- [🔗Optional Parameters in Callbacks (공식문서)](https://www.typescriptlang.org/docs/handbook/declaration-files/do-s-and-don-ts.html#optional-parameters-in-callbacks)
- [🔗[번역]콜백에서 선택적 매개변수 (Optional Parameters in Callbacks) (공식문서)](https://www.typescriptlang.org/ko/docs/handbook/declaration-files/do-s-and-don-ts.html#콜백에서-선택적-매개변수-optional-parameters-in-callbacks)
