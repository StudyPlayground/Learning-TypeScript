# CHAPTER 7 인터페이스

## 7.1 타입 별칭 vs 인터페이스

- 차이점
  - 인터페이스는 속성 증가를 위해 병합할 수 있습니다. 이 기능은 내장된 전역 인터페이스 또는 npm 패키지와 같은 외부 코드를 사용할 때 특히 유용합니다.
  - 인터페이스는 클래스가 선언된 구조의 타입을 확인하는데 사용할 수 있지만 타입 별칭은 사용할 수 없습니다.
  - 일반적으로 인터페이스에서 타입스크립트 타입 검사기가 더 빨리 작동합니다. 인터페이스는 타입 별칭이 하는 것처럼 새로운 객체 리터럴의 동적인 복사 붙여넣기보다 내부적으로 더 쉽게 캐시할 수 있는 명명된 타입을 선언합니다.
  - 인터페이스는 이름 없는 객체 리터럴의 별칭이 아닌 이름이 있는 객체로 간주되므로 어려운 특이 케이스에서 나타나는 오류 메시지를 좀 더 쉽게 읽을 수 있습니다.

## 7.2 속성 타입

- 선택적 속성
  ```jsx
  interface Book {
    author?: string;
    pages: number;
  }
  ```
- 읽기 전용 속성
  - readonly 제한자는 타입 시스템에만 존재하며 인터페이스에서만 사용할 수 있습니다. readonly 제한자는 객체의 인터페이스를 선언하는 위치에서만 사용되고 실제 객체에는 적용되지 않습니다.
  - readonly는 단지 타입스크립트 타입 검사기를 사용해 개발 중에 그 속성이 수정되지 못하도록 보호하는 역할을 합니다.
  ```jsx
  interface Page {
  	readonly text: string;
  }
  ```
- 함수와 메서드
  - 인터페이스 멤버를 함수로 선언하는 두 가지 방법
    - 메서드 구문 : 인터페이스 멤버를 member(): void와 같이 객체의 멤버로 호출되는 함수로 선언
    - 속성 구문 : 인터페이스의 멤버를 member: () ⇒ void와 같이 독립 함수와 동일하게 선언
    ```jsx
    interface HasBothFunctionTypes {
      property: () => string;
      method(): string;
    }

    const hasBoth: HasBothFunctionTypes = {
      property: () => "",
      method() {
        return "";
      },
    };
    ```
  - 메서드와 속성 선언은 대부분 서로 교환하여 사용할 수 있습니다.
  - 메서드와 속성의 주요 차이점
    - 메서드는 readonly로 선언할 수 없지만 속성은 가능합니다.
    - 인터페이스 병합은 메서드와 속성을 다르게 처리합니다.
    - 타입에서 수행되는 일부 작업은 메서드와 속성을 다르게 처리합니다.
  - 권장되는 스타일 가이드
    - 기본 함수가 this를 참조할 수 있다는 것을 알고 있다면 메서드 함수를 사용하세요. 가장 일반적으로 클래스의 인스턴스에서 사용됩니다.
    - 반대의 경우는 속성 함수를 사용하세요.
- 호출 시그니처
  - 호출 시그니처가 선언한 방식으로 호출되는 값만 인터페이스에 할당할 수 있습니다. 즉, 할당 가능한 매개변수와 반환 타입을 가진 함수입니다.
  - 호출 시그니처는 사용자 정의 속성을 추가로 갖는 함수를 설명하는 데 사용할 수 있습니다. 타입스크립트는 함수 선언에 추가된 속성을 해당 함수 선언의 타입에 추가하는 것으로 인식합니다.
    ```jsx
    interface FunctionWithCount {
      count: number;
      (): void;
    }

    let hasCallCount: FunctionWithCount;

    function keepsTrackOfCalls() {
      keepsTrackOfCalls.count += 1;
      console.log(keepsTrackOfCalls.count);
    }

    keepsTrackOfCalls.count = 0;

    hasCallCount = keepsTrackOfCalls;

    function doesNotHaveCount() {
      console.log("No Count");
    }

    hasCallCount = doesNotHaveCount;
    // ERROR : Property 'count' is missing in type '() => void' but required in type 'FunctionWithCalls'
    ```
- 인덱스 시그니처
  - 타입스크립트는 인덱스 시그니처 구문을 제공해 인터페이스의 객체가 임의의 키를 받고, 해당 키 아래의 특정 타입을 반환할 수 있음을 나타냅니다.
    ```jsx
    interface WordCounts {
      [i: string]: number;
    }
    ```
  - 인덱스 시그니처는 객체에 값을 할당할 때 편리하지만 타입 안정성을 완벽하게 보장하지는 않습니다. 인덱스 시그니처는 객체가 어떤 속성에 접근하든 간에 값을 반환해야 함을 나타냅니다.
    ```jsx
    interface DatesByName {
      [i: string]: number;
    }

    const publishDates: DatesByName = {
      Frankenstein: new Date("1 January 1818"),
    };

    publishDates.Frankenstein; // type is Date
    consol.log(publishDates.Frankenstein.toString()); // OK

    publishDates.Beloves; // type is Date, but undefined in runtime
    console.log(publishDates.Beloved.toString()); // ERROR in runtime
    ```
  - 키, 값 쌍을 저장하려고 하는데 키를 미리 알 수 없다면 Map을 사용하는 편이 더 안전합니다.
  - 속성과 인덱스 시그니처 혼합
    - 인터페이스는 명시적으로 명명된 속성과 포괄적인 용도의 string 인덱스 시그니처를 한번에 포함할 수 있습니다. 명명된 속성이 더 구체적인 타입을 제공하고, 다른 모든 속성은 인덱스 시그니처의 타입으로 대체하는 것으로 혼합해서 사용할 수 있습니다.
      - 속성과 인덱스 시그니처를 혼합해서 사용하는 일반적인 타입 시스템 기법 중 하나는 인덱스 시그니처의 원시 속성보다 명명된 속성에 대해 더 구체적인 속성 타입 리터럴을 사용하는 것입니다.
        ```jsx
        interface ChapterStarts {
          preface: 0;
          [i: string]: number;
        }
        ```
  - 숫자 인덱스 시그니처
    - 자바스크립트가 암묵적으로 객체 속성 조회 키를 문자열로 변환하지만 때로는 객체의 키로 숫자만 허용하는 것이 바람직할 수 있습니다. 타입스크립트 인덱스 시그니처는 키로 string 대신 number 타입을 사용할 수 있지만, 명명된 속성은 그 타입을 포괄적인 용도의 string 인덱스 시그니처의 타입으로 할당할 수 있어야 합니다.
      ```jsx
      interface MorNarrowNumbers {
        [i: number]: string;
        [i: string]: string | undefined;
      }
      ```
  - 중첩 인터페이스
    ```jsx
    interface Novel {
      author: {
        name: string,
      };
      setting: Setting;
    }

    interface Setting {
      place: string;
      year: number;
    }
    ```

## 7.3 인터페이스 확장

- 타입스크립트는 인터페이스가 다른 인터페이스의 모든 멤버를 복사해서 선언할 수 있는 확장된 인터페이스를 허용합니다. 확장할 인터페이스의 이름 뒤에 extends 키워드를 추가해서 다른 인터페이스를 확장한 인터페이스라는 걸 표시합니다.
  ```jsx
  interface Writing {
    title: string;
  }

  interface Novella extends Writing {
    pages: number;
  }
  ```
- 재정의된 속성
  - 파생 인터페이스는 다른 타입으로 속성을 다시 선언해 기본 인터페이스의 속성을 재정의하거나 대체할 수 있습니다.
  - 속성을 재선언하는 대부분의 파생 인터페이스는 해당 속성을 유니언 타입의 더 구체적인 하위 집합으로 만들거나 속성을 기본 인터페이스의 타입에서 확장된 타입으로 만들기 위해 사용합니다.
    ```jsx
    interface Withnullablename {
      name: string | null;
    }

    interface WithnonNullableName extends WithNullablename {
      name: string;
    }
    ```
- 다중 인터페이스 확장
  ```jsx
  interface GivesNumber {
    giveNumber(): number;
  }

  interface GivesString {
    giveString(): string;
  }

  interface GivesBothAndEither extends GivesNumber, GivesString {
    giveEither(): number | string;
  }
  ```

## 7.4 인터페이스 병합

- 두 개의 인터페이스가 동일한 이름으로 동일한 스코프에 선언된 경우, 선언된 모든 필드를 포함하는 더 큰 인터페이스가 코드에 추가됩니다.
  ```jsx
  interface Merged {
    fromFirst: string;
  }

  interface Merged {
    fromSecond: number;
  }
  ```
- 일반적인 타입스크립트 개발에서는 인터페이스 병합을 자주 사용하지는 않습니다. 인터페이스가 여러 곳에 선언되면 코드를 이해하기가 어려워지므로 가능하면 인터페이스 병합을 사용하지 않는 것이 좋습니다.
  - 그러나 인터페이스 병합은 외부 패키지 또는 Window 같은 내장된 전역 인터페이스를 보강하는 데 특히 유용합니다.
    ```jsx
    interface Window {
      myEnvironmentVariable: string;
    }

    window.myEnvironmentVariable; // Type is string
    ```
- 이름이 충돌되는 멤버
  - 병합된 인터페이스는 타입이 다른 동일한 이름의 속성을 여러 번 선언할 수 없습니다. 속성이 이미 인터페이스에 선언되어 있다면 나중에 병합된 인터페이스에서도 동일한 타입을 사용해야 합니다.
    ```jsx
    interface MergedProperties {
      same: (input: boolean) => string;
      different: (input: string) => string;
    }

    interface MergedProperties {
      same: (input: boolean) => string;
      different: (input: number) => string;
      // ERROR : Subsequent property declarations must have the same type.
    }
    ```
  - 그러나 병합된 인터페이스는 동일한 이름과 다른 시그니처를 가진 메서드는 정의할 수 있습니다. 이렇게 하면 메서드에 대한 함수 오버로드가 발생합니다.
    ```jsx
    interface MergedMethods {
      differnet(input: string): string;
    }

    interface MergedMethods {
      different(input: number): string;
    }
    ```

## 7.5 마치며
