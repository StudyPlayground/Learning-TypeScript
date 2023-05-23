# CHAPTER 15 타입 운영

## 15.1 매핑된 타입

- 타입스크립트는 다른 타입의 속성을 기반으로 새로운 타입을 생성하는 구문을 제공합니다. 즉, 하나의 타입에서 다른 타입으로 매핑`Mapping`합니다. 타입스크립트의 매핑된 타입은 다른 타입을 가져와서 해당 타입의 각 속성에 대해 일부 작업을 수행하는 타입입니다.

  ```tsx
  type Animals = "alligator" | "baboon" | "cat";

  type AnimalCounts = {
    [K in Animals]: number;
  };

  // {
  //     alligator: number;
  //     baboon: number;
  //     cat: number;
  // }
  ```

- 타입에서 매핑된 타입

  - 일반적으로 매핑된 타입은 존재하는 타입에 keyof 연산자를 사용해 키를 가져오는 방식으로 작동합니다. 존재하는 타입의 키를 매핑하도록 타이벵 지시하면 새로운 타입으로 매핑합니다.

    ```tsx
    interface AnimalVariants {
      alligator: boolean;
      baboon: number;
      cat: string;
    }

    type AnimalCounts = {
      [K in keyof AnimalVariants]: AnimalCounts[K] | null;
    };

    // {
    //     alligator: boolean | null;
    //     baboon: number | null;
    //     cat: string | null;
    // }
    ```

- 매핑된 타입과 시그니처

  - 7장 인터페이스에서 타입스크립트가 인터페이스 멤버를 함수로 선언하는 두 가지 방법을 제공한다고 소개했습니다.
    - `member(): void` 같은 메서드 구문: 인터페이스의 멤버가 객체의 멤버로 호출되도록 의도된 함수임을 선언
    - `member: () ⇒ void` 같은 속성 구문: 인터페이스의 멤버가 독립 실행형 함수와 같다고 선언
  - 매핑된 타입은 객체 타입의 메서드와 속성 구문을 구분하지 않습니다. 매핑된 타입은 메서드를 원래 타입의 속성으로 취급합니다.

    ```tsx
    interface Researcher {
      researchMethod(): void;
      researchProperty: () => void;
    }

    type JustProperties<T> = {
      [K in keyof T]: T[K];
    };

    type ResearcherProperties = JustProperties<Researcher>;

    // {
    //     researchMethod: () => void;
    //     researchProperties: () => string;
    // }
    ```

- 제한자 변경

  - 매핑된 타입은 원래 타입의 멤버에 대해 접근 제어 제한자인 `readonly`와 `?`도 변경 가능합니다. 전형적인 인터페이스와 동일한 구문을 사용해 매핑된 타입의 멤버에 `readonly`와 `?`를 배치할 수 있습니다.

    ```tsx
    interface Environmentalist {
      area: string;
      name: string;
    }

    type ReadOnlyEnvironmentalist = {
      readonly [K in keyof Environmentalist]: Environmentalist[K];
    };

    // {
    //     readonly area: string;
    //     readonly name: string;
    // }

    type OptionalReadonlyEnvironmentalist = {
      [K in keyof ReadOnlyEnvironmentalist]?: ReadOnlyEnvironmentalist[K];
    };

    // {
    //     readonly area?: string;
    //     readonly name?: string;
    // }
    ```

  - 새로운 타입의 제한자 앞에 `-`를 추가해 제한자를 제거합니다. `readonly`나 `?`을 작성하는 대신 `-readonly` 또는 `-?`을 사용합니다.

    ```tsx
    interface Conservationist {
        name: string;
        catchphrase?: string;
        readonly born: number;
        readonly died?: number;
    }

    type WritableConservationist {
        -readonly [K in keyof Conservationist]: Conservationist[K];
    }

    // {
    //     name: string;
    //     catchphrase?: string;
    //     born: number;
    //     died?: number;
    // }

    type RequiredWritableConservationist = {
        [K in keyof WritableConservationist]-?: WritableConservationist[K];
    }

    // {
    //     name: string;
    //     catchphrase: string;
    //     born: number;
    //     died: number;
    // }
    ```

- 제네릭 매핑된 타입

  - 매핑된 타입의 완전한 힘은 제네릭과 결합해 단일 타입의 매핑을 다른 타입에서 재사용할 수 있도록 하는 것에서 나옵니다. 매핑된 타입은 매핑된 타입 자체의 타입 매개변수를 포함해 keyof로 해당 스코프에 있는 모든 타입 이름에 접근할 수 있습니다.
  - 개발자들이 일반적으로 표현해야 하는 또 다른 변환은 임의의 수의 인터페이스를 받고, 그 인터페이스의 완전히 채워진 인스턴스를 반환하는 함수입니다.

    ```tsx
    interface GenusData {
      family: string;
      name: string;
    }

    type MakeOptional<T> = {
      [K in keyof T]?: T[K];
    };

    // {
    //     family?: string;
    //     name?: string;
    // }

    function createGenusData(overrides?: MakeOptional<GenusData>): GenusData {
      return {
        family: "unknown",
        name: "unknown",
        ...overrides,
      };
    }
    ```

## 15.2 조건부 타입

- 타입스크립트의 타입 시스템은 이전 타입에 대한 논리적인 검사를 바탕으로 새로운 구성(타입)을 생성합니다. `조건부 타입`의 개념은 기존 타입을 바탕으로 두 가지 가능한 타입 중 하나로 확인되는 타입입니다.
  - 조건부 타입에서 논리적 검사는 항상 `extends`의 왼쪽 타입이 오른쪽 타입이 되는지 또는 할당 가능한지 여부에 있습니다.
    ```tsx
    LeftType extends RightType ? IfTrue : IfFalse
    ```
  - 코드 스니펫이 더 복잡해질수록 각 조건부 타입은 순전히 boolean 로직의 일부라는 것을 기억하세요.
- 제네릭 조건부 타입

  - 모든 다른 타입을 기반으로 새로운 타입을 생성하기 위해 재사용 가능한 제네릭 타입을 작성할 수 있습니다.

    ```tsx
    type CheckAgainstNumber<T> = T extends number ? true : false;

    type CheckString1 = CheckAgainstNumber<"string">;
    // false

    type CheckString2 = CheckAgainstNumber<1891>;
    // true
    ```

  - 자바스크립트 라이브러리에서 사용하는 패턴 중 조건부 제네릭 타입에도 적합한 한 가지 패턴은 함수에 제공된 옵션 객체를 기반으로 함수의 반환 타입을 변경하는 것입니다.

    ```tsx
    interface QueryOptions {
      throwIfNotFound: boolean;
    }

    type QueryResult<Options extends QueryOptions> =
      Options["throwIfNotFound"] extends true ? string : string | undefined;

    declare function retrieve<Options extends QueryOptions>(
      key: string,
      options?: Options
    ): Promise<QueryResult<Options>>;

    await retrieve("string");
    // return type is string | undefined

    await retrieve("Jane", { throwIfNotFound: true });
    // return type is string
    ```

- 타입 분산

  - 조건부 타입은 유니언에 분산`distribute`됩니다. 결과 타입은 각 구성 요소에 조건부 타입을 적용하는 유니언이 됨을 의미합니다. 즉, `CoditionalType<T | U>`는 `Conditional<T> | Conditional<U>`와 같습니다.
  - 타입스크립트의 조건부 타입이 유니언에 분산되지 않는다면 `string | number`는 `string`에 할당할 수 없기 때문에 HalfArrayified는 `(string | number)[]`가 됩니다. 즉, 조건부 타입은 전체 유니언 타입이 아니라 유니언 타입의 각 구성 요소에 로직을 적용합니다.

    ```tsx
    type ArrayifyUnlessString<T> = T extends string ? T : T[];

    type HalfArrayified = ArrayifyUnlessString<string | number>;
    // string | number[]
    ```

- 유추된 타입

  - 제공된 타입의 멤버에 접근하는 것은 타입의 멤버로 저장된 정보에 대해서는 잘 작동하지만 함수 매개변수 또는 반환 타입과 같은 다른 정보에 대해서는 알 수 없습니다. 조건부 타입은 extends 절에 `infer 키워드`를 사용해 조건의 임의의 부분에 접근합니다. extends 절에 타입에 대한 infer 키워드와 새 이름을 배치하면 조건부 타입이 true인 경우 새로운 타입을 사용할 수 있음을 의미합니다.

    ```tsx
    type ArrayItems<T> = T extends (infer Item)[] ? Item : T;

    type StringItem = ArrayItems<string>;
    // string

    type StringArrayItem = ArrayItems<string[]>;
    // string

    type String2DItem = ArrayItems<string[][]>;
    // string[]
    ```

- 매핑된 조건부 타입

  - 매핑된 타입은 기존 타입의 모든 멤버에 변경 사항을 적용하고 조건부 타입은 하나의 기존 타입에 변경 사항을 적용합니다. 이 둘을 함께 사용하면 제네릭 템플릿 타입의 각 멤버에 조건부 로직을 적용할 수 있습니다.

    ```tsx
    type MakeAllMembersFunctions<T> = {
      [K in keyof T]: T[K] extends (...args: any[]) => any ? T[K] : () => T[K];
    };

    type MemberFunctions = MakeAllMembersFunctions<{
      alreadyFunction: () => string;
      notYetFunction: number;
    }>;

    // {
    //     alreadyFunction: () => string,
    //     notYetFunction: () => number,
    // }
    ```

## 15.3 never

- never와 교차, 유니언 타입
  - 교차 타입`&`에 있는 never는 교차 타입을 never로 만듭니다.
  - 유니언 타입`|`에 있는 never는 무시됩니다.
    - 조건부 타입과 매핑된 타입에서 값을 필터링하는 데 유용합니다.
- never와 조건부 타입

  - 제네릭 조건부 타입은 일반적으로 유니언에서 타입을 필터링하기 위해 never를 사용합니다. never는 유니언에서 무시되기 때문에 유니언 타입에서 제네릭 조건부의 결과는 never가 아닌 것이 됩니다.

    ```tsx
    type OnlyStrings<T> = T extends string ? T : never;

    type RedOrBlue = OnlyStrings<"red" | "blue" | 0 | false>;
    // "red" | "blue"
    ```

  - never는 또한 제네릭 타입에 대한 타입 유틸리티를 만들 때 유추된 조건부 타입과 결합됩니다. infer가 있는 타입 추론은 조건부 타입이 true가 되어야 하므로 false인 경우를 절대 사용하지 않아야 합니다. 바로 이때 never를 사용하면 적합합니다.

    ```tsx
    type FirstParameter<T extends (...args: any[]) => any> = T extends (
      arg: infer Arg
    ) => any
      ? Arg
      : never;

    type GetsString = FirstParameter<(arg0: string) => void>;
    // string
    ```

- never와 매핑된 타입

  - 유니언에서 never의 동작은 매핑된 타입에서 멤버를 필터링할 때도 유용합니다. 다음 세가지 타입 시스템 기능을 사용해 객체의 키를 필터링합니다.
    - 유니언에서 never는 무시됩니다.
    - 매핑된 타입은 타입의 멤버를 매핑할 수 있습니다.
    - 조건부 타입은 조건이 충족되는 경우 타입을 never로 변환하는 데 사용할 수 있습니다.
  - 세 가지 기능을 함께 사용하면 원래 타입의 각 멤버를 원래 키 또는 never로 변경하는 매핑된 타입을 만들 수 있습니다. [keyof T]로 해당 타입의 멤버를 요청하면 모든 매핑된 타입의 결과 유니언이 생성되고 never는 필터링됩니다.
  - OnlyStringProperties<T> 타입을 읽는 또 다른 방법은 string이 아닌 모든 속성을 필터링 한 다음 나머지 모든 키`[keyof T]`를 반환하는 것입니다.

    ```tsx
    type OnlyStringProperties<T> = {
      [K in keyof T]: T[K] extends string ? K : never;
    }[keyof T];

    interface AllEventData {
      participants: string[];
      location: string;
      name: string;
      year: number;
    }

    type OnlyStringEventData = OnlyStringProperties<AllEventData>;
    // "location" | "name"
    ```

## 15.4 템플릿 리터럴 타입

- 문자열 타입이 패턴에 맞는지를 나타내는 타입스크립트 구문인 템플릿 리터럴 타입을 입력해보세요. 템플릿 리터럴 타입은 템플릿 리터럴 문자열처럼 보이지만 추정할 수 있는 원시 타입 또는 원시 타입 유니언이 있습니다.
  ```tsx
  type Greeting = `Hello${string}`;

  let matches: Greeting = "Hello, world";

  let outOfOrder: Greeting = "World! Hello!";
  // ERROR: Type '"World! Hello!"' is not assignable to type '`Hello${string}`'.
  ```
- 템플릿 리터럴 타입을 더 좁은 문자열 패턴으로 제한하기 위해 포괄적인 string 원시 타입 대신 문자열 리터럴 타입과 그 유니언을 타입 보간법에 사용할 수 있습니다. 템플릿 리터럴 타입은 제한된 허용 문자열 집합과 일치해야 하는 문자열을 설명하는 데 매우 유용합니다.
  ```tsx
  type Brightness = "dark" | "light";
  type Color = "blue" | "red";

  type BrightnessAndColor = `${Brightness}-${Color}`;
  // "dark-blue" | "dark-red" | "light-blue" | "light-red"
  ```
- 타입스크립트는 템플릿 리터럴 타입이 string, number, bigint, boolean, null, undefined와 같은 모든 원시 타입(symbol 제외) 또는 그 조합을 포함하도록 허용합니다.
- 고유 문자열 조작 타입
  - 문자열 타입 작업을 지원하기 위해 타입스크립트는 문자열을 가져와 문자열에 일부 조작을 적용하는 고유 (타입스크립트에 내장된) 제네릭 유틸리티 타입을 제공합니다.
    - Uppercase: 문자열 리터럴 타입을 대문자로 변환합니다.
    - Lowercase: 문자열 리터럴 타입을 소문자로 변환합니다.
    - Capitalize: 문자열 리터럴 타입의 첫 번째 문자를 대문자로 변환합니다.
    - Uncapitalize: 문자열 리터럴 타입의 첫 번째 문자를 소문자로 변환합니다.
- 템플릿 리터럴 키
  - 템플릿 리터럴 타입은 문자열 리터럴을 사용할 수 있는 모든 위치에서 사용 가능합니다.
    ```tsx
    type DataKey = "location" | "name" | "year";

    type ExistenceChecks = {
      [K in `check${Capitalize<DataKey>}`]: () => boolean;
    };

    // {
    //     checkLocation: () => boolean;
    //     checkName: () => boolean;
    //     checkYear: () => boolean;
    // }
    ```
- 매핑된 타입 키 다시 매핑하기
  - 타입스크립트는 템플릿 리터럴 타입을 사용해 원래 멤버를 기반으로 매핑된 타입의 멤버에 대한 새로운 키를 생성할 수 있습니다. 매핑된 타입에서 인덱스 시그니처에 대한 템플릿 리터럴 타입 다음에 as 키워드를 배치하면 결과 타입의 키는 템플릿 리터럴 타입과 일치하도록 변경됩니다. 이렇게 하면 매핑된 타입은 원래 값을 계속 참조하면서 각 매핑된 타입 속성에 대한 다른 키를 가질 수 있습니다.
    ```tsx
    interface DataEntry<T> {
      key: T;
      value: string;
    }

    type DataKey = "location" | "name" | "year";

    type DataEntryGetters = {
      [K in DataKey as `get${Capitalize<K>}`]: () => DataEntry<K>;
    };

    // {
    //     getLocation: () => DataEntry<"location">;
    //     getName: () => DataEntry<"name">;
    //     getYear: () => DataEntry<"year">;
    // }
    ```
  - 키를 다시 매핑하는 작업과 다른 타입 운영을 결합해 기존 타입 형태를 기반으로 하는 매핑된 타입을 생성할 수 있습니다. 한 가지 재미있는 조합은 기존 객체에 keyof typeof를 사용해 해당 객체의 타입에서 매핑된 타입을 만드는 것입니다.
    ```tsx
    const config = {
      locaiton: "unknown",
      name: "anonymous",
      year: 0,
    };

    type LazyValues = {
      [K in keyof typeof config as `${K}Lazy`]: () => Promise<
        (typeof config)[K]
      >;
    };

    // {
    //     location: Promise<string>;
    //     name: Promise<string>;
    //     year: Promise<number>;
    // }
    ```
  - 자바스크립트에서 객체 키는 string 또는 Symbol이 될 수 있고, Symbol 키는 원시 타입이 아니므로 템플릿 리터럴 타입으로 사용할 수 없습니다. 제네릭 타입에서 다시 매핑된 템플릿 리터럴 타입 키를 사용하려고 하면 타입스크립트는 템플릿 리터럴 타입에서 symbol을 사용할 수 없다는 오류를 발생시킵니다.
    ```tsx
    type TurnIntoGettersDirect<T> = {
      [K in keyof T as `get${K}`]: () => T[K];
      // ERROR: Type 'symbol' is not assignable to type 'string | number | bigint | boolean | null | undefined'.
    };
    ```
  - 이러한 제한 사항을 피하기 위해 string과 교차 타입`&`을 사용하여 문자열이 될 수 있는 타입만 사용하도록 강제합니다. string & symbol은 never가 되므로 전체 템플릿 문자열은 never가 되고 타입스크립트는 이를 무시하게 됩니다.
    ```tsx
    const someSymbol = Symbol("");

    interface HasStringAndSymbol {
      StringKey: string;
      [someSymbol]: number;
    }

    type TurnIntoGetters<T> = {
      [K in keyof T as `get${string & K}`]: () => T[K];
    };

    type GettersJustString = TurnIntoGetters<HasStringAndSymbol>;

    // {
    //     getStringKey: () => string;
    // }
    ```

## 15.5 타입 운영과 복잡성

## 15.6 마치며
