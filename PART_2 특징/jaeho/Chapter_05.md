# CHAPTER 5 함수

## 5.1 함수 매개변수

- 필수 매개변수
- 선택적 매개변수
  - 타입 애너테이션의 `:` 앞에 `?`를 추가해 매개변수가 선택적이라고 표시합니다.
  - 선택적 매개변수에는 항상 `| undefined`가 유니언 타입으로 추가되어 있습니다.
  - 선택적 매개변수는 `| undefined`를 포함하는 유니언 타입 매개변수와는 다릅니다.
    - `?`으로 표시된 선택적 매개변수가 아닌 매개변수는 값이 명시적으로 `undefined`일지라도 항상 제공되어야 합니다.
    ```tsx
    function announceSongBy(song: string, singer: string | undefined) {}

    announceSongBy("Greensleeves"); // ERROR : Expected 2 arguments, but got 1.
    announceSongBy("Greensleeves", undefined);
    ```
  - 함수에서 사용되는 모든 선택적 매개변수는 마지막 매개변수여야 합니다.
- 기본 매개변수
  - 매개변수에 기본값이 있고 타입 애너테이션이 없는 경우, 타입스크립트는 해당 기본값을 기반으로 매개변수 타입을 유추합니다.
  - 다음 rateSong 함수에서 rating은 `number` 타입으로 유추되지만, 함수를 호출하는 코드에서는 선택적 `number | undefined`가 됩니다.
    ```tsx
    function rateSong(song: string, rating = 0) {}

    rateSong("Photograph");
    rateSong("Photograph", 5);
    rateSong("Photograph", undefined);
    rateSong("Photograph", "100"); // ERROR : Argument of type "100" is not assignable to parameter of type 'number | undefined'.
    ```
- 나머지 매개변수

## 5.2 반환 타입

- 함수가 다른 값을 가진 여러 개의 반환문을 포함하고 있다면, 타입스크립트는 방환 타입을 가능한 모든 반환 타입의 조합으로 유추합니다.
- 명시적 반환 타입
  - 변수와 마찬가지로 타입 애너테이션을 사용해 함수의 반환 타입을 명시적으로 선언하지 않는 것이 좋습니다.
  - 그러나 특히 함수에서 반환 타입을 명시적으로 선언하는 방식이 매우 유용할 때가 종종 있습니다.
    - 가능한 반환값이 많은 함수가 항상 동일한 타입의 값을 반환하도록 강제합니다.
    - 타입스크립트는 재귀 함수의 반환 타입을 통해 타입을 유추하는 것을 거부합니다.
    - 수백 개 이상의 타입스크립트 파일이 있는 큰 프로젝트에서 타입스크립트 타입 검사 속도를 높일 수 있습니다.

## 5.3 함수 타입

- 함수 타입은 콜백 매개변수를 설명하는 데 자주 사용됩니다.
- 함수 타입 괄호
  ```tsx
  // 타입은 string | undefined 유니언을 반환하는 함수
  let returnStringOrUndefined: () => string | undefined;

  // 타입은 undefined나 string을 반환하는 함수
  let maybeReturnsString: (() => string) | undefined;
  ```
- 매개변수 타입 추론
  - 선언된 타입의 위치에 제공된 함수의 매개변수 타입을 유추할 수 있습니다.
  ```tsx
  let singer: (song: string) => string;

  singer = function (song) {
    // song: string
  };
  ```

## 5.4 그 외 반환 타입

- void 반환 타입
  - 함수 타입을 선언할 때 void를 사용하면 함수에서 반환되는 모든 값은 무시됩니다.
  - 자바스크립트 함수는 실젯값이 반환되지 않으면 기본으로 모두 undefined를 반환하지만 void는 undefined와 동일하지 않습니다. void는 함수의 반환 타입이 무시된다는 것을 의미하고 undefined는 반환되는 리터럴 값입니다. undefined를 포함하는 대신 void 타입의 값을 할당하려고 하면 타입 오류가 발생합니다.
    ```tsx
    function returnsVoid() {
      return;
    }

    let lazyValue: string | undefined;
    lazyValue = returnsVoid(); // ERROR : Type 'void' is not assignable to type 'string | undefined'.
    ```
  - undefined와 void를 구분해서 사용하면 매우 유용합니다. 특히 void를 반환하도록 선언된 타입 위치에 전달된 함수가 반환된 모든 값을 무시하도록 설정할 때 유용합니다.
  - void 타입은 자바스크립트가 아닌 함수의 반환 타입을 선언하는 데 사용하는 타입스크립트 키워드입니다. void 타입은 함수의 반환값이 자체적으로 반환될 수 있는 값도 아니고, 사용하기 위한 것도 아니라는 표시임을 기억하세요.
- never 반환 타입
  - never 반환 함수는 (의도적으로) 항상 오류를 발생시키거나 무한 루프를 실행하는 함수입니다.
  - 함수가 절대 반환하지 않도록 의도하려면 명시적 `: never` 타입 애너테이션을 추가해 해당 함수를 호출한 후 모든 코드가 실행되지 않음을 나타냅니다.
- never는 void와는 다릅니다. void는 아무것도 반환하지 않는 함수를 위한 것이고, never는 절대 반환하지 않는 함수를 위한 것입니다.

## 5.5 함수 오버로드

- 오버로드 시그니처 (Overload Signature)
  - 선택적 매개변수와 나머지 매개변수만으로 표현할 수 없는 매우 다른 매개변수들로 호출될 수 있는 함수
- 구현 시그니처 (implementation signature)
  - 함수의 내부 로직이 작성된 함수
- 오버로드된 함수 호출에 대해 구문 오류를 생성할 지 여부를 결정할 때 타입스크립트는 함수의 오버로드 시그니처만 확인합니다. 구현 시그니처는 함수의 내부 로직에서만 사용됩니다.
- 함수 오버로드는 복잡하고 설명하기 어려운 함수 타입에 사용하는 최후의 수단입니다. 함수를 단순하게 유지하고 가능하면 함수 오버로드를 사용하지 않는 것이 좋습니다.
- 구현 시그니처는 모든 오버로드 시그니처와 호환되어야 합니다.
  ```tsx
  function format(data: string): string;
  function format(data: string, needle: string, haystack: string): string;

  function format(getData: () => string): string;
  // ERROR : This overload signature is not compatible with its implementation signature.

  function format(data: string, needle?: string, haystack?: string) {
    return needle && haystack ? data.replace(needle, haystack) : data;
  }
  ```

## 5.6 마치며
