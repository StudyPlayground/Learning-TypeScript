# CHAPTER 11 선언 파일

- 타입스크립트는 구현과 별도로 타입 형태를 선언할 수 있습니다. 타입 선언은 파일 이름이 `.d.ts` 확장자로 끝나는 선언 파일에 작성됩니다. 선언 파일은 일반적으로 프로젝트 내에서 작성되고, 프로젝트의 컴파일된 npm 페키지로 빌드 및 배포되거나 독립 실행형 typings 패키지로 공유됩니다.

## 11.1 선언 파일

- .d.ts 파일은 자바스크립트로 컴파일할 수 있는 모든 런타임 코드를 포함할 수 없습니다.
- 선언 파일은 다른 타입스크립트 파일과 마찬가지로 import해서 사용할 수 있습니다.
- 선언 파일은 값이 아닌 타입만 선언할 수 있는 코드 영역을 의미하는 앰비언트 컨텍스트 `ambient context` 를 생성합니다.

## 11.2 런타임 값 선언

- 비록 선언 파일은 함수 또는 변수 같은 런타임 값을 생성하지 않을 수 있지만, declare 키워드를 사용해 이러한 구조체가 존재한다고 선언할 수 있습니다. 이렇게 하면 웹 페이지의 script 태그 같은 일부 외부 작업이 특정 타입의 이름을 사용해 값을 생성했음을 타입 시스템에 알립니다.
- declare로 변수를 선언하면 초깃값이 허용되지 않는다는 점을 제외하고 일반적인 변수 선언과 동일한 구문을 사용합니다.
- 함수와 클래스도 일반적인 형식과 유사하게 선언되지만 함수 또는 메서드의 본문이 없습니다.
  ```tsx
  declare let declared: string;

  declare function canGrantWish(wish: string): boolean;

  class Fairy {
    canGrantWish(wish: string): boolean;
  }
  ```
- 전역 변수
  - import, export 문이 없는 타입스크립트 파일은 모듈이 아닌 스크립트로 취급되기 때문에 여기에 선언된 타입을 포함한 구문은 전역으로 사용됩니다.
  - 전역으로 선언된 값은 전역 변수를 사용하는 브라우저 애플리케이션에서 가장 자주 사용됩니다.
    ```tsx
    // globals.d.ts
    declare const version: string;

    // version.ts
    export function getVersion() {
      return version;
    }
    ```
  - 전역 인터페이스 병합
    - 전역 API와 값에 대한 많은 타입 선언이 전역으로 존재합니다. 인터페이스는 동일한 이름의 다른 인터페이스와 병합되기 때문에 import와 export 문이 없는 .d.ts 선언 파일 같은 전역 스크립트 컨텍스트에서 인터페이스를 선언하면 해당 인터페이스가 전역으로 확장됩니다.
      ```tsx
      // types/window.d.ts
      interface Window {
        myVersion: string;
      }

      // index.ts
      export function logWindowVersion() {
        console.log(window.myVersion);
        window.alert(window.myVersion);
      }
      ```
  - 전역 확장
    - 경우에 따라서 모듈 파일에 선언된 타입이 전역으로 사용되어야 합니다.
    - 타입스크립트에서 declare global 코드 블록 구문을 사용해 해당 블록 내용이 전역 컨텍스트에 있다고 표시합니다.
      ```tsx
      // types/data.d.ts
      export interface Data {
        version: string;
      }

      // types/globals.d.ts
      import { Data } from "./data";

      declare global {
        // (전역 컨텍스트)
        const globallyDeclared: Data;
      }

      // (모듈 컨텍스트)
      declare const locallyDeclared: Data;

      // index.ts
      globallyDeclared;
      locallyDeclared; // ERROR : Cannot find name 'locallyDeclared';
      ```

## 11.3 내장된 선언

- Array, Function, Map, Set과 같은 전역 객체는 타입 시스템이 알아야 하지만 코드에서 선언되지 않는 구문입니다. 이와 같은 전역 객체는 디노, Node.js, 웹 브라우저 등에서 실행되는 런타임 코드에 의해 제공됩니다.
- 라이브러리 선언
  - 모든 자바스크립트 런타임에 존재하는 Array, Function 같은 내장된 전역 객체는 lib.[target].d.ts 파일 이름으로 선언됩니다. 여기서 target은 ES5, ES202, ESNext와 같은 프로젝트에서 대상으로 하는 자바스크립트의 최소 지원 버전입니다.
  - 내장된 라이브러리 선언 파일 또는 `lib` 파일은 자바스크립트의 내장된 API 전체를 나타내기 때문에 상당히 큽니다.
  - lib 파일은 타입스크립트 npm 패키지의 일부로 배포되며 `node_modules/typescript/lib.es5.d.ts`와 같은 경로의 패키지 내부에서 찾을 수 있습니다.
  - 라이브러리 target
    - 타입스크립트는 기본적으로 tsc CLI 또는 프로젝트의 tsconfig.json에서 제공된 target 설정에 따라 적절한 lib 파일을 포함합니다. 자바스크립트 최신 버전에 대한 연속적인 lib 파일들은 인터페이스 병합을 사용해 서로 빌드됩니다.
    - target보다 최신 버전의 자바스크립트에서만 사용할 수 있는 기능은 타입 시스템에서 사용할 수 없습니다.
- DOM 선언
  - DOM 타입은 lib.dom.d.ts 파일과 다른 lib.\*.d.ts 선언 파일에도 저장됩니다.
  - lib 컴파일러 옵션을 재정의하지 않은 타입스크립트 프로젝트는 DOM 타입을 기본으로 포함합니다.

## 11.4 모듈 선언

- 선언 파일의 또 다른 중요한 기능은 모듈의 상태를 설명하는 기능입니다. 모듈의 문자열 이름 앞에 declare 키워드를 사용하면 모듈의 내용을 타입 시스템에 알릴 수 있습니다.
  ```tsx
  // modules.d.ts
  declare module "my-example-lib" {
    export const value: string;
  }

  // index.ts
  import { value } from "my-example-lib";

  console.log(value);
  ```
- 코드에서 `declare module`을 자주 사용해서는 안 됩니다. declare module은 주로 다음 절에 나오는 와일드카드 모듈 선언과 이 장의 후반부에서 다루는 패키지 타입과 함께 사용됩니다.
- 와일드카드 모듈 선언
  - 모듈 선언은 JS, TS 파일 확장자가 아닌 특정 파일의 내용을 코드로 가져올 수 있음을 웹 애플리케이션에 알리기 위해 사용합니다. 모듈 선언으로 하나의 \* 와일드카드를 포함해 해당 패턴과 일치하는 모든 모듈을 나타낼 수 있습니다.
    ```tsx
    // styles.d.ts
    declare module "*.module.css" {
      const styles: { [i: string]: string };
      export default styles;
    }

    // component.ts
    import styles from "./styles.module.css";

    styles.anyClassName; // string
    ```
    - 와일드카드 모듈을 사용해 로컬 파일을 나타내는 방식이 타입 안정성을 완벽히 보장하지는 않습니다. 타입스크립트는 가져온 모듈 경로가 로컬 파일과 일치하는지 확인하는 메커니즘을 제공하지 않습니다.

## 11.5 패키지 타입

- 패키지 간에 타입을 사용하는 방법
  - 타입스크립트로 작성된 프로젝트는 여전히 .js로 컴파일된 파일이 포함된 패키지를 배포합니다. 일반적으로 .d.ts 파일을 사용해 이러한 자바스크립트 파일 뒤에 타입스크립트 타입 시스템 형태를 지원하도록 선언합니다.
- 선언
  - 타입스크립트는 입력된 파일에 대한 .d.ts 출력 파일과 자바스크립트 출력 파일을 함께 생성하는 선언 옵션을 제공합니다.
  - 자동으로 생성된 .d.ts 파일은 프로젝트에서 사용자가 사용할 타입 정의를 생성하는 가장 좋은 방법입니다.
- 패키지 타입 의존성
  - 타입스크립트는 프로젝트의 node_modules 의존성 내부에서 번들로 제공되는 .d.ts 파일을 감지하고 활용할 수 있습니다.
  - jest 패키지 내에 자체 번들 .d.ts 파일을 제공합니다. describe, it 같은 함수를 제공하는 `@jest/globals` 패키지에 대한 의존성을 가지며 jest는 전역으로 사용할 수 있습니다.
  - `@jest/globals` 패키지는 describe, it을 내보냅니다. 그런 다음 jest 패키지는 해당 함수를 가져오고, 해당 함수 타입의 describe, it 변수를 가지고 전역 스코프로 확장합니다.
- 패키지 타입 노출
  - 프로젝트가 npm에 배포되고 사용자를 위한 타입을 제공하려면 패키지의 package.json 파일에 `types`필드를 추가해 루트 선언 파일을 가리킵니다. types 필드는 main 필드와 유사하게 작동하고 종종 동일한 것처럼 보이지만, .js 확장자 대신에 .d.ts 확장자를 사용합니다.
  - types 필드가 패키지의 package.json에 존재하지 않으면, 타입스크립트는 ./index.d.ts를 기본값으로 가정합니다. 이런 방식은 types 필드가 지정되지 않은 경우 ./index.js 파일을 패키지의 기본 진입점으로 가정하는 npm의 기본 동작을 반영한 것입니다.
  - 대부분의 패키지는 타입스크립트의 선언 컴파일러 옵션을 사용해 소스 파일로부터 .js 파일과 함께 .d.ts 파일을 생성합니다.

## 11.6 DefinitelyTyped

- 타입스크립트 프로젝트는 여전히 해당 패키지에서 모듈의 타입 형태를 알려줘야 합니다. 타입스크립트 팀과 커뮤니티는 커뮤니티에서 작성된 패키지 정의를 수용하기 위해 DefinitelyTyped (이하 DT)라는 거대한 저장소를 만들었습니다.
- DT 패키지는 타입을 제공하는 패키지와 동일한 이름으로 npm에 @types 범위로 게시됩니다.
- 프로젝트가 npm 패키지로 배포되어야 하는 경우, dependencies를 사용해야만 패키지를 사용하는 곳에서 사용되는 타입 정의를 가져올 수 있습니다. 만약 프로젝트가 서버에서 빌드 및 실행되는 독립 실행형 애플리케이션이라면 devDependencies를 사용해 타입이 단지 개발 시 사용되는 툴임을 전달해야 합니다.
- 타입 없는 패키지 경우, 다음과 같이 3가지 방법으로 타입을 얻을 수 있습니다.
  - @types/ 패키지를 생성하기 위해 DT에 pull request를 보냅니다.
  - declare module 구문을 사용해 프로젝트 내에서 타입을 작성합니다.
  - 구성 옵션에서 noImplicitAny 옵션을 비활성하고 강력하게 경고합니다.

## 11.7 마치며
