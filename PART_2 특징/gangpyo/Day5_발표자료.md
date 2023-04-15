# 타입호환성 관점에서의 인덱스 시그니처와 brand타입
## 인덱스 시그니처

임의의 속성을 허용하는 속성을 의미하며, 타입스크립트의 타입호환성의 확장을 의미하기도한다.

**적용 전**

```tsx
interface Person {
  name: string;
}

const person: Person = {
  name: "john",
  gender: "male" // error
	nationality: "US" // error
}
```

**적용 후**

```tsx
interface Person {
  name: string;
  [i:string]: string; // 인덱스 시그니처 선언
}

const me: Person = {
  name: "john",
  gender: "male", // ok
	nationality: "US" // ok
}
```

→ 기존 name 필드만을 허용한 Person 인터페이스를 키: string, 값:string타입의 속성에한하여 호환을 확장시켰다. 

## **brand타입**

__brand와같은 임의의 속성을 의미하며, 타입스크립트의 타입호환성의 축소를 의미하기도한다.

**적용 전**

```tsx
// 화씨
type Celsius = number;
// 섭씨
type Fahrenheit = number;

function convertToFahrenheit(celsius: Celsius): Fahrenheit {
    return celsius * 9 / 5 + 32;
}

const testCelsius: Celsius = 200;
const testFahrenheit: Fahrenheit = 392;

convertToFahrenheit(testCelsius);
convertToFahrenheit(testFahrenheit); // 에러가 발생하지 않는다.(타입이 호환된다)
```

**적용 후**

```tsx
// 화씨
type Celsius = number & {__brand: "Celsius"}; 

// 섭씨
type Fahrenheit = number & {__brand: "Fahrenheit"};

function convertToFahrenheit(celsius: Celsius): Fahrenheit {
    return celsius * 9 / 5 + 32 as Fahrenheit;
}

const testCelsius = 200 as Celsius;
const testFahrenheit = 392 as  Fahrenheit;

convertToFahrenheit(testCelsius);
convertToFahrenheit(testFahrenheit); // error
```

→ 동일한 타입이지만 의미가 다른(ex. 섭씨 ↔화씨, 원화 ↔ 달러 등등)타입을 다룰때 좀더 안전하게 다룰 수 있게 해준다.

### 결론

타입스크립트는 개발환경에따라 개발자가 상황에맞게 타입의 호환성범위를 조정할 수 있도록 선택지를 제공해준다.

# Reference

[Documentation - Type Compatibility](https://www.typescriptlang.org/ko/docs/handbook/type-compatibility.html)

[Surviving the TypeScript Ecosystem — Part 6: Branding and Type-Tagging](https://medium.com/@KevinBGreene/surviving-the-typescript-ecosystem-branding-and-type-tagging-6cf6e516523d)

[TypeScript 타입 시스템 뜯어보기: 타입 호환성](https://toss.tech/article/typescript-type-compatibility)