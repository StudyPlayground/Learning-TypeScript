---
marp: true
paginate: true
---

# 타입 건전성 (Type Soundness)

1. 건전성이란?
2. Typescript 불건전성 종류

---

# 건전성 (Soundness)

> 정의

- 컴파일러가 컴파일하는 동안뿐만 아니라 런타임에 값이 갖는 Type에 대해 보장할 수 있다는 것이다.
- 처음부터 Type으로 빌드된 대부분의 프로그래밍 언어에서 일반적이다.

---

# 건전성 (Soundness)

> Typescript 건전성

- 기존 언어를 모델링하는 Type 시스템을 구축하는 것은 단순성, 사용성 및 건전성이라는 세 가지 특성을 절충하여 결정한다.
- TypeScript의 목표는 `모든 JavaScript 코드를 지원`이다.
- 따라서 Typescript는 컴파일 시점에 알기 어려운 특정 Type을 안전하다고 간주한다.

---

# Typescript 불건전성 종류

> 대표 예시

1. any
2. Type Assertions
3. 객체 및 배열 조회

---

# Typescript 불건전성 종류

> any

```tsx
function alertNumber(x: number) {
  alert(x.toFixed(1));
}
const num: any = 'forty two';
// num type is any

alertNumber(num);
// ERROR : x.toFixed is not a function
```

---

# Typescript 불건전성 종류

> Type Assertions

```tsx
const usersAge = ("23" as any) as number;
// usersAge type is number
```

- 타입이 잘못 추론되거나 Typescript에게 타입을 재정의하도록 하는 경우

---

# Typescript 불건전성 종류

> 객체 및 배열 조회

```tsx
const xs = [1, 2, 3];
const x = xs[3];
// x type is string

alert(x.toFixed(1));
// ERROR : Cannot read properties of undefined (reading 'toFixed')
```

---

# 참고

- [The Seven Sources of Unsoundness in TypeScript](https://effectivetypescript.com/2021/05/06/unsoundness/)
- [건전성 - 프리온보딩 프론트엔드 챌린지 (23년02월)](https://pollen-port-115.notion.site/d8a0dc3e3477443eb7c65e46717a8932)
- [Typescript Soundness (Typescript playground)](https://www.typescriptlang.org/play?strictFunctionTypes=false&q=184#example/soundness)