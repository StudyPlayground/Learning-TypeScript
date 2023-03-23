# CHAPTER 6 배열

## 6.1 배열 타입

- 배열 타입은 `Array<number>` 같은 구문으로도 작성할 수 있습니다. 하지만 개발자 대부분은 더 간단한 `number[]`를 선호합니다.
- any 배열의 진화
  - 초기에 빈 배열로 설정된 변수에서 타입 애너테이션을 포함하지 않으면 타입스크립트는 배열을 `any[]`로 취급하고 모든 콘텐츠를 받을 수 있습니다.
  ```
  let values = [];
  // let values: any[]

  values.push('hello');
  values
  // let values: string[]

  values[0] = null;
  values
  // let values: (string | null)[]

  values.push(0);
  values
  // let values: (string | null | number)[]
  ```
- 다차원 배열
  - n차원 배열은 n개의 `[]`를 갖습니다.
  ```jsx
  let array2: number[][];
  let array2: number[][];
  let array3: number[][][];
  ```

## 6.2 배열 멤버

- 배열 타입은 `Array<number>` 같은 구문으로도 작성할 수 있습니다. 하지만 개발자 대부분은 더 간단한 `number[]`를 선호합니다.
- any 배열의 진화
  - 초기에 빈 배열로 설정된 변수에서 타입 애너테이션을 포함하지 않으면 타입스크립트는 배열을 `any[]`로 취급하고 모든 콘텐츠를 받을 수 있습니다.
  ```
  let values = [];
  // let values: any[]

  values.push('hello');
  values
  // let values: string[]

  values[0] = null;
  values
  // let values: (string | null)[]

  values.push(0);
  values
  // let values: (string | null | number)[]
  ```
- 다차원 배열
  - n차원 배열은 n개의 `[]`를 갖습니다.
  ```jsx
  let array2: number[][];
  let array2: number[][];
  let array3: number[][][];
  ```

## 6.3 스프레드와 나머지 매개변수

- 스프레드
  - 스프레드 연산자를 사용해 배열을 결합합니다. 타입스크립트는 입력된 배열 중 하나의 값이 결과 배열에 포함될 것임을 이해합니다.
- 나머지 매개변수
  - 타입스크립트는 나머지 매개변수로 배열을 스프레드하는 자바스크립트 실행을 인식하고 이에 대해 타입 검사를 수행합니다. 나머지 매개변수를 위한 인수로 사용되는 배열은 나머지 매개변수와 동일한 배열 타입을 가져야 합니다.

## 6.4 튜플

- `튜플`이란 고정된 크기의 배열입니다.
- 튜플 배열은 `각 인덱스에 알려진 특정 타입`을 가지며 배열의 모든 가능성 멤버를 갖는 유니언 타입보다 더 구체적입니다.
  ```
  let yearAndWarrior: [number, string];

  yearAndWarrior = [530, "Tomyris"];

  yearAndWarrior = [false, "Tomyris"];
  // ERROR : Type 'boolean' is not assignable to type 'number'.

  yearAndWarrior = [530];
  // ERROR : Type '[number]' is not assignable to type '[number, string]'.
  ```
- 자바스크립트에서는 단일 조건을 기반으로 두 개의 변수에 초깃값을 설정하는 것처럼 한 번에 여러 값을 할당하기 위해 튜플과 배열 구조 분해 할당을 함께 자주 사용합니다.
  ```
  let [year, warrior] = Math.random() > 0.5
      ? [340, "Archidamia"]
      : [1828, "Rani of Jhansi"];
  ```
- `가변 길이의 배열 타입`은 튜플 타입에 할당할 수 없습니다.
  ```
  const pairLoose = [false, 123];
  // pairLoose: (boolean | number)[]

  const pairTupleLoose: [boolean, number] = pairLoose;
  // ERROR : Type '(number | string)[]' is not assignable to type '[boolean, number]'.
  ```
- 타입스크립트는 …나머지 매개변수로 전달된 튜플에 정확한 타입 검사를 제공할 수 있습니다.
  ```
  function logPair(name: string, value: number) {
      console.log(`${name} has ${value}`);
  }

  const pairArray = ["Amage", 1];
  logPair(...pairArray);
  // ERROR : A spread argument must either have a tuple type or be passed to a rest parameter.

  const pairTupleIncorrect: [number, string] = [1, "Amage"];
  logPair(...pairTupleIncorrect);
  // ERROR : Argument of type 'number' is not assignable to parameter of type 'string'.

  const pairTupleCorrect: [string, number] = ["Amage", 1];
  logPair(...pairTupleCorrect);
  ```
- 타입스크립트는 생성된 배열을 튜플이 아닌 `가변 길이의 배열`로 취급합니다. 배열이 변수의 초깃값 또는 함수에 대한 반환값으로 사용되는 경우, 고정된 크기의 튜플이 아니라 `유연한 크기의 배열`로 가정합니다.
  - 타입스크립트에서는 값이 일반적인 배열 타입 대신 좀 더 구체적인 튜플타입이어야 함을 다음 2가지 방법으로 나타냅니다.
  - `명시적 튜플 타입`
    - 함수가 튜플 타입을 반환한다고 선언되고, 배열 리터럴을 반환한다면 해당 배열 리터럴은 일반적인 가변 길이의 배열 대신 튜플로 간주됩니다.
      ```
      function firstCharAndSizeExplicit (input: string) : [string, number] {
          return [input[0], input.length];
      }

      const [firstChar, size] = firstCharAndSizeExplicit("Cathay Williams");
      // firstChar: string
      // size: number

      const value = firstCharAndSizeExplicit("Cathay Williams");
      value[0] = "change" // OK
      ```
  - `const 어서션`
    - 명시적 타입 애너테이션에 튜플 타입을 입력하는 작업은 코드 변경에 따라 작성 및 수정이 필요한 구문을 추가하는 번거로움이 있습니다.
    - 그 대안으로 타입스크립트는 값 뒤에 넣을 수 있는 const 어서션인 `as const` 연산자를 제공합니다. const 어서션은 타입스크립트에 타입을 유추할 때 `읽기 전용`이 가능한 값 형식을 사용하도록 지식합니다.
    - const 어서션은 유연한 크기의 배열을 `고정된 크기의 튜플로 전환`하는 것을 넘어서, 해당 튜플이 읽기 전용이고, `값 수정`이 예상되는 곳에서 사용할 수 없음을 나타냅니다.
      ```
      const pairMutable: [number, string] = [1157, "Tomoe"];
      pairMutable[0] = 1247;

      const pairAlsoMutable: [number, string] = [1157, "Tomoe"] as const;
      // ERROR : The type 'readonly [1157, "Tomoe"]' is 'readonly' and cannot be assigned to the mutable type '[number, string]'.

      const pairConst = [1157, "Tomoe"] as const;
      pairConst[0] = 1247;
      // ERROR : Cannot assign to '0' because it is a read-only property.
      ```
    - 실제로 읽기 전용 튜플은 함수 반환에 편리합니다. 튜플을 반환하는 함수로부터 반환된 값은 보통 즉시 구조화되지 않으므로 읽기 전용인 튜플은 함수를 사용하는 데 방해가 되지 않습니다.
      ```
      function firstCharAndSizeAsConst (input: string) {
          return [input[0], input.length] as const;
      }

      const [firstChar, size] = firstCharAndSizeAsConst("Ching Shih");
      // const firstChar: string
      // const size: number

      const value = firstCharAndSizeAsConst("Ching Shih");
      value[0] = "change"
      // ERROR : Cannot assign to '0' because it is a read-only property.
      ```

## 6.5 마치며
