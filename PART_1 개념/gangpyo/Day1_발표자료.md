<!-- Day01_발표자료 -->
# any vs unknown 타입
any타입과 unknown타입 모두 모든 타입을 수용할 수 있으나, unknown타입은 좀더 세부적인 타입을 필요로 한다.

## 1. any

- any타입은 원시타입, 객체, 배열, 함수, 에러, 심볼 등 가능한 모든 JavaScript 값을 나타낸다.
- 슈퍼타입

```jsx
let value: any;

value = true; // OK
value = 42; // OK
value = "Hello World"; // OK
value = []; // OK
value = {}; // OK
value = Math.random; // OK
value = null; // OK
value = undefined; // OK
value = new TypeError(); // OK
value = Symbol("type"); // OK
```

```jsx
let value: any;

value.foo.bar; // OK
value.trim(); // OK
value(); // OK
new value(); // OK
value[0][1]; // OK
```

→ 코드를 작성하기엔 쉬우나, 그만큼 런타임에 오류가 발생할 확률이 올라간다.

→ 남용하게되면 타입스크립트를 사용하는 이유가 없어진다.

## 2. unknown

- any타입과 동일하게 모든타입을 unknown타입에 할당할 수 있다.

```jsx
let value: unknown;

value = true; // OK
value = 42; // OK
value = "Hello World"; // OK
value = []; // OK
value = {}; // OK
value = Math.random; // OK
value = null; // OK
value = undefined; // OK
value = new TypeError(); // OK
value = Symbol("type"); // OK
```

- 변수에 할당하는것은 any타입과 동일

```jsx
let value: unknown;

let value1: unknown = value; // OK
let value2: any = value; // OK
let value3: boolean = value; // Error
let value4: number = value; // Error
let value5: string = value; // Error
let value6: object = value; // Error
let value7: any[] = value; // Error
let value8: Function = value; // Error
```

- unknown타입의 변수를 다른 타입의 변수에 할당하게되면 에러 발생.(any타입변수 제외)

```jsx
let value: unknown;

value.foo.bar; // Error
value.trim(); // Error
value(); // Error
new value(); // Error
value[0][1]; // Error
```

- any와 다르게 연산을 수행하게될시 에러 발생
- 연산을 수행할 수있는 두가지 방법.
    1. 타입 좁히기
        
        ```jsx
        let value: unknown;
        
        value = "hello";
        
        if(typeof value === "string") {
          value.trim() // OK
          }
        ```
        
    2. 타입강제
        
        ```jsx
        let value: unknown;
        
        value = "hello";
        
        let temp = value as string
        
        temp.trim() // OK
        ```
        
        - 타입스크립트는 타입에대해 개발자가 더 잘 알고 있다고 판단하여 별다른 검사를 수행하지 않게됨.
        - 실수로 잘못된 타입으로 강제할 경우 런타임 오류가 발생하기 쉽다.

### 2.1 Union Types

```jsx
type UnionType1 = unknown | null; // unknown
type UnionType2 = unknown | undefined; // unknown
type UnionType3 = unknown | string; // unknown
type UnionType4 = unknown | number[]; // unknown

type UnionType5 = unknown | any; // any
```

### 2.2 Intersection Types

```jsx
type IntersectionType1 = unknown & null; // null
type IntersectionType2 = unknown & undefined; // undefined
type IntersectionType3 = unknown & string; // string
type IntersectionType4 = unknown & number[]; // number[]
type IntersectionType5 = unknown & any; // any
```

## Reference

[The unknown Type in TypeScript](https://mariusschulz.com/blog/the-unknown-type-in-typescript)