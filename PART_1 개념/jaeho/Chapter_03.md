# CHAPTER 3 유니언과 리터럴

## 3.1 유니언 타입

- 유니언 (union)
  - 값에 허용된 타입을 두 개 이상의 가능한 타입으로 확장하는 것
- 유니언 타입 선언은 타입 애너테이션으로 타입을 정의하는 모든 곳에서 사용할 수 있습니다.
- 유니언 타입 선언의 순서는 중요하지 않습니다.
- 값이 유니언 타입일 때 타입스크립트는 `유니언으로 선언한 모든 가능한 타입에 존재하는 멤버 속성`에만 접근할 수 있습니다.

  ```tsx
  let value = Math.random() > 0.5 ? "over half" : 99;

  value.toString(); // OK

  value.toUpperCase(); // ERROR : number타입에 없는 멤버라서
  value.toFixed(); // ERROR : string타입에 없는 멤버라서
  ```

  - 유니언 타입으로 정의된 여러 타입 중 하나의 타입으로 된 값의 속성을 사용하려면 코드에서 값이 보다 구체적인 타입 중 하나라는 것을 타입스크립트에 알려야 합니다. ⇒ 내로잉

## 3.2 내로잉

- 내로잉 (narrowing)
  - 값에 허용된 타입이 하나 이상의 가능한 타입이 되지 않도록 좁히는 것
- 값이 정의, 선언 혹은 이전에 유추된 것보다 더 구체적인 타입임을 코드에서 유추하는 것입니다.
- 타입 가드
  - 타입을 좁히는 데 사용할 수 있는 논리적 검사
- 내로잉 방법

  - 값 할당을 통한 내로잉

    ```tsx
    let value: number | string;
    value = "Typescript";

    value.toUpperCase(); // OK
    value.toFixed(); // ERROR string으로 내로잉 되었기 때문에 string에는 해당 멤버가 없다.
    ```

  - 조건 검사를 통한 내로잉

    ```tsx
    let value = Math.random() > 0.5 ? "over half" : 99;

    if (value === "over half") {
      value.toUpperCase(); // OK
    }

    value.toUpperCase(); // ERROR : ERROR : number타입에 없는 멤버라서
    ```

  - typeof 검사를 통한 내로잉

    ```tsx
    let value = Math.random() > 0.5 ? "over half" : 99;

    if (typeof value === "string") {
      value.toUpperCase(); // OK
    }
    ```

## 3.3 리터럴 타입

- 원시 타입 값 중 어떤 것이 아닌 `특정 원싯값`으로 알려진 타입이 리터럴 타입입니다.
- 만약 변수를 `const로 선언`하고 `직접 리터럴 값을 할당`하면 타입스크립트는 해당 변수를 할당된 리터럴 값으로 유추합니다.
- 원시 타입은 해당 타입의 가능한 모든 리터럴 값의 집합입니다.
  - 그러므로 리터럴 타입은 그 값이 해당하는 원시 타입에 할당할 수 있습니다.

## 3.4 엄격한 null 검사

- 타입스크립트 컴파일러 옵션의 `strictNullChecks`를 활성화하는 것을 권장한다.
  - 활성화해야만 코드가 null 또는 undefined 값으로 인한 오류로부터 안전한지 여부를 쉽게 파악할 수 있습니다.
- 참 검사를 통한 내로잉
  - 자바스크립트에서 `truthy`, `falsy`(false, 0, -0, 0n, “”, null, undefined)가 있다.
  - 다음 코드에서 value는 false | string 타입이고, if문에서 stirng으로 좁힐 수 있지만, else 문에서 value가 빈 문자열인 경우에는 여전히 string이 될 수 있음을 알 수 있습니다.
    ```tsx
    let value = Math.random() > 0.5 && "over half";

    if (value) {
      value; // type is string
    } else {
      value; // type is string | false
    }
    ```

## 3.5 타입 별칭

- 재사용하는 타입에 더 쉬운 이름을 할당하는 타입 별칭이 있습니다.
- 타입 별칭은 순전히 타입스크립트 타입 시스템에만 존재하므로, 자바스크립트로 컴파일되지 않습니다.
  - 또한 런타입 코드에서는 참조할 수 없습니다.
- 타입 별칭은 다른 타입 별칭을 참조할 수 있습니다.
  - 사용 순서대로 타입 별칭을 선언할 필요는 없습니다.

## 3.6 마치며
