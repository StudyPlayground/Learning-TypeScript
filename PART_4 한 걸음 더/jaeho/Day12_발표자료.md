---
marp: true
paginate: true
---

# 퀴즈

---

## 퀴즈 1

### `BrightnessAndColor`의 타입을 작성해주세요.

```typescript
type Brightness = "dark" | "light";
type Color = "blue" | "red";

type BrightnessAndColor = `${Brightness extends "dark" ? "1" : "2"}-${Color}`;
```

---

## 퀴즈 2

### `LazyValues`코드가 오류가 납니다. 이유와 수정내용을 작성해주세요.

```typescript
const config = {
  locaiton: "unknown",
  name: "anonymous",
  year: 0,
};

type LazyValues = {
  [K in keyof config]: K extends "unknown" ? "stringValue" : never;
};
```

---

## 퀴즈 3

### `newType`의 타입을 작성해주세요.

```typescript
interface I1 {
  nickname: string;
  age: number;
}

interface I2 {
  nickname: string;
  gender: boolean;
}

type newType = {
  [K in keyof I1 & I2]: I1[K];
};
```

---

## 참고

- Chapter15: 타입 운영
- [퀴즈 1](https://www.typescriptlang.org/play?#code/C4TwDgpgBAQgTgSwOYAtgDsIGctQLxQBEAJgIZwDWhUAPkQDbJqEDcAUKJFAMID29vOPiIAjegFcI1OoTgRirNh3DR4TDNiwBBdMT4ChBAAYASAN5rUGnFAgAPYBF24S5KlAD8RAIzUAXEQATIQAvgC05vqCIUbsQA)
- [퀴즈 2](https://www.typescriptlang.org/play?#code/MYewdgzgLgBKYDMCWBzGBeGBvAUDfMANiMAIZJTgBcMARAK5gDWYIA7mLQDR4FikBbAKY1apVmACeAkPQjde+SUNIAnGgAYeAXwDcOHFEkAHITAAypAF6SAaqUL0hEDNkUwA2gGkYSMDCYhSRAEOHBkFABdGh8hAA8oITAAExcGZlYOWhgAfjpoVT8Ue0chbJowIQA3IVUcPQMAekaYQFemwAlRnGaYI1MLazsHJxdMXG6CTx8-AKCQnpMhOfgI6JhYhKTUukYWdk5c-KhCsGKhspgK6tquloagA)
- [퀴즈 3](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAgJIEZkG8BQz-IjAIDWIcAthAFzIDOYUoA5gNx4FzM2ECuFAI2jsAvjhyhIsRClQAmbB3xFS5KrQZMQbJcm4gAJtFoCA9qYA2EOCFHiwATwAOKEBADuAFWcoAvIoJkAG0AaWRQZBIIB1MYNEwAMjQ5AF1aDFCU0SA)
