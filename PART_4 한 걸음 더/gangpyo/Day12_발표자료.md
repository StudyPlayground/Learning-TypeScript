# 타입스크립트의 enum은 tree-shaking되지 않는다.

## enum?

요소,멤버라 불리는 명명된 값의 집합을 이루는 자료형을 의미. - 위키 백과 -

**객체와의 차이?** 

- 객체는 코드내에서 새로운 속성을 자유롭게 추가할 수 있지만, enum 은 선언할때 이후에 변경할 수 없다.
- 객체의 속성값은 JS가 허용하는 모든 타입이 올 수 있지만, enum 의 속성값으로는 문자열 혹은 숫자만 허용된다.

## Tree-shaking?

사용하지 않는 코드를 삭제하는 기능을 의미. 나무를 흔들면 죽은 잎사귀들이 떨어지는 모습에 착안해 Tree-shaking이라고 부른다. Tree-shaking을 통해 export했지만 아무 데서도 import하지 않은 모듈이나 사용하지 않는 코드를 삭제해서 번들 크기를 줄여 페이지가 표시되는 시간을 단축할 수 있다.

## enum과 Tree-shaking이 무슨 연관성이 있나?

enum은 자바스크립트에는 없고 타입스크립트에서만 존재한다.따라서 트랜스파일러에의해 TS → JS로 변환되는 과정에서 즉시실행 함수(IIFE)를 사용하게된다.

**트랜스파일 전**

```tsx
enum fruit {
  apple,
  pere,
  orange
}
```

**트랜스파일 후**

```tsx
"use strict";
var fruit;
(function (fruit) {
    fruit[fruit["apple"] = 0] = "apple";
    fruit[fruit["pere"] = 1] = "pere";
    fruit[fruit["orange"] = 2] = "orange";
})(fruit || (fruit = {}));
```

문제는 여기서 발생하는데, Rollup과 같은 번들러는 즉시실행 함수(IIFE)를 ‘사용하지 않는 코드’라고 판단할 수 없어서 해당 코드를Tree-shaking하지 않게된다는 것.

[rollup의 예제코드](https://rollupjs.org/repl/?version=2.26.11&shareable=JTdCJTIyZXhhbXBsZSUyMiUzQW51bGwlMkMlMjJtb2R1bGVzJTIyJTNBJTVCJTdCJTIybmFtZSUyMiUzQSUyMm1haW4uanMlMjIlMkMlMjJjb2RlJTIyJTNBJTIyaW1wb3J0JTIwJTdCRGlyZWN0aW9uJTdEJTIwZnJvbSUyMCcuJTJGZW51bS5qcyclNUNuaW1wb3J0JTIwJTdCaGVsbG8lN0QlMjBmcm9tJTIwJy4lMkZ0cmVlc2hha2VkJyU1Q25pbXBvcnQlMjAlN0JoaSU3RCUyMGZyb20lMjAnLiUyRm5vdFRyZWVzaGFrZWQnJTVDbiU1Q25jb25zb2xlLmxvZyhoaSklNUNuJTVDbiUyMiUyQyUyMmlzRW50cnklMjIlM0F0cnVlJTdEJTJDJTdCJTIybmFtZSUyMiUzQSUyMmVudW0uanMlMjIlMkMlMjJjb2RlJTIyJTNBJTIyJ3VzZSUyMHN0cmljdCclNUNuZXhwb3J0JTIwdmFyJTIwRGlyZWN0aW9uJTVDbiUzQihmdW5jdGlvbiUyMChEaXJlY3Rpb24pJTIwJTdCJTVDbiUyMCUyMERpcmVjdGlvbiU1QidVcCclNUQlMjAlM0QlMjAnVVAnJTVDbiUyMCUyMERpcmVjdGlvbiU1QidEb3duJyU1RCUyMCUzRCUyMCdET1dOJyU1Q24lMjAlMjBEaXJlY3Rpb24lNUInTGVmdCclNUQlMjAlM0QlMjAnTEVGVCclNUNuJTIwJTIwRGlyZWN0aW9uJTVCJ1JpZ2h0JyU1RCUyMCUzRCUyMCdSSUdIVCclNUNuJTdEKShEaXJlY3Rpb24lMjAlN0MlN0MlMjAoRGlyZWN0aW9uJTIwJTNEJTIwJTdCJTdEKSklMjIlN0QlMkMlN0IlMjJuYW1lJTIyJTNBJTIydHJlZXNoYWtlZC5qcyUyMiUyQyUyMmNvZGUlMjIlM0ElMjJleHBvcnQlMjB2YXIlMjBoZWxsbyUyMCUzRCUyMCdoZWxsbyclMjIlN0QlMkMlN0IlMjJuYW1lJTIyJTNBJTIybm90VHJlZXNoYWtlZC5qcyUyMiUyQyUyMmNvZGUlMjIlM0ElMjJleHBvcnQlMjB2YXIlMjBoaSUyMCUzRCUyMCdoaSclMjIlN0QlNUQlMkMlMjJvcHRpb25zJTIyJTNBJTdCJTdEJTdE)

## const enum을 사용하면 어떨까?

**트랜스파일 전**

```tsx
const enum fruit {
  apple,
  pere,
  orange
}
```

**트랜스파일 후**

```tsx
"use strict";
```

tree-shaking문제가 발생하진 않지만, 다음문제가 발생한다.

1. **const enum은 바벨을통해 트랜스파일링을 할 수 없다.** 
    - 따라서 ”babel-plugin-const-enum”라는 별도의 플러그인을 설치해줘야 한다.
2. **ambient context에서 문제가 될 수 있다.**
    - isolatedModules 컴파일 옵션 활성시, ambient context에서 선언된 const enum을 다른 모듈에서 접근하면 에러가 발생한다.
        
        ```tsx
        // example.d.ts
        declare const enum fruit {
          apple,
          pere,
          orange
        }
        ```
        
        ```tsx
        // someModule.ts
        fruit.orange; // error:Cannot access ambient const enums when the '--isolatedModules' flag is provided.
        ```
        

## 결론

위에서 소개한 오류 및 enum이라는 개념자체가 타입스크립트의“a typed superset of JavaScript” 개념에 맞지기 때문에 enum사용을 지양하고 대신에union을 사용하자. 

```tsx
const fruit = {
  apple: "Apple",
  pere: "Pare",
  orange: "Orange"
} as const;

type Fruit = typeof fruit[keyof typeof fruit]; 
// 타입: "Apple" | "Pare" | "Orange"
```

## 참고자료

[열거형](https://ko.wikipedia.org/wiki/열거형)

[Typescript enum, enum vs object](https://dkrnfls.tistory.com/276)

[さようなら、TypeScript enum - 株式会社カブク](https://www.kabuku.co.jp/developers/good-bye-typescript-enum)

[TypeScript enum을 사용하지 않는 게 좋은 이유를 Tree-shaking 관점에서 소개합니다.](https://engineering.linecorp.com/ko/blog/typescript-enum-tree-shaking)

[TSConfig Reference - Docs on every TSConfig option](https://www.typescriptlang.org/tsconfig#isolatedModules)