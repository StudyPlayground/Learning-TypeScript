# CHAPTER 13 구성 옵션

- 다음 방법을 통해 타입스크립트의 구성 옵션을 설정할 수 있습니다.
  - tsc에 전달된 명령줄 (CLI) 플래그
  - 타입스크립트 구성 파일 TSConfig

## 13.1 tsc 옵션

- pretty 모드
  - 출력 터미널의 여러 색상 텍스트를 지원하여 가독성을 높여줍니다.
- watch 모드
  - 종료하는 대신 타입스크립트를 무기한 실행 상태로 유지하고 모든 오류의 실시간 목록을 가져와서 터미널을 지속적으로 업데이트합니다.

## 13.2 TSConfig 파일

- tsconfig.json 파일은 해당 디렉터리가 타입스크립트 프로젝트의 루트임을 나타냅니다.
- `tsc —init`
  - tsconfig.json 파일을 생성하기 위한 명령어입니다.
- `CLI` vs `tsconfig.json`
  - CLI, tsconfig.json 파일에서 사용 가능한 대부분의 옵션은 다음 두 가지 범주 중 하나로 분류됩니다.
    - 컴파일러 : 포함된 각 파일이 타입스크립트에 따라 컴파일되거나 타입을 확인하는 방법
    - 파일 : 타입스크립트가 실행될 파일과 실행되지 않은 파일

## 13.3 파일 포함

- 기본적으로 tsc는 현재 디렉터리와 하위 디렉터리에 있는 숨겨지지 않은 모든 .ts 파일에서 실행되고, 숨겨진 디렉터리와 node_modules 디렉터리는 무시합니다.
- tsconfig.json은 실행할 파일 목록을 수정할 수 있습니다.
  - include
    - 타입스크립트 컴파일에 포함할 디렉터리와 파일을 설명하는 문자열 배열을 명시합니다.
  - exclude
    - include 파일 목록에 타입스크립트로 컴파일할 수 없는 파일이 포함되는 경우 작성합니다.

## 13.4 대체 확장자

- JSX 구문
  - jsx
  - .tsx 파일의 제네릭 화살표 함수
- resolveJsonModule
  - .json 파일을 읽을 수 있습니다.

## 13.5 자바스크립트로 내보내기

- 바벨 같은 전용 컴파일러 도구의 등장으로 일부 프로젝트에서는 타입스크립트의 역할이 타입 검사만으로 축소되었지만, 타입스크립트 구문을 자바스크립트로 컴파일하기 위해 여전히 타입스크립트에 의존하고 있는 프로젝트도 많습니다. 프로젝트가 타입스크립트에 단일 의존성을 갖고, tsc 명령을 사용해 자바스크립트를 출력하는 작업은 매우 유용합니다.
- outDir
  - 기본적으로 출력 파일을 해당 소스 파일과 동일한 위치에 생성합니다.
  - 출력 파일의 루트 디렉터리를 다르게 지정하면 입력 파일과 동일한 디렉터리 구조를 유지해서 출력합니다.
- target
  - 자바스크립트 코드 구문을 지원하기 위해 어느 버전까지 변환해야 하는지를 지정합니다.
- 내보내기 선언
  - 대부분의 패키지는 타입스크립트의 declaration 컴파일러 옵션을 사용해 소스 파일에서 .d.ts 출력 파일을 내보냅니다.
  - emitDeclarationOnly
- 소스 맵
  - 출력 파일의 내용이 원본 소스 파일과 어떻게 일치하는지에 대한 설명입니다.
  - 출력 파일을 탐색할 때 디버거 같은 개발자 도구에서 원본 소스 코드를 표시하도록 설정합니다.
  - sourceMap
  - declarationMap
- noEmit
  - 다른 도구를 이용해 소스 파일을 컴파일하고, 자바스크립트를 출력하는 프로젝트에서 타입스크립트는 파일 생성을 모두 건너뛰도록 지시할 수 있습니다.
  - 해당 옵션을 활성화하면 온전히 타입 검사기로만 작동합니다.

## 13.6 타입 검사

- lib
  - 타입스크립트가 런타임 환경에 있다고 가정하는 전역 API는 lib 컴파일러 옵션으로 구성할 수 있습니다.
  - 해당 설정을 변경하는 유일한 이유는 브라우저에서 실행되지 않는 프로젝트에서 기본으로 포함된 dom을 제거하기 위함 입니다.
- skipLibCheck
  - 소스 코드에 명시적으로 포함되지 않은 선언 파일에서 타입 검사를 건너뛰도록 하는 옵션입니다.
  - 공유된 라이브러리의 정의가 서로 다르고 충돌할 수 있는 패키지 의존성이 많이 사용하는 애플리케이션에 유용합니다.
- 엄격 모드
  - noImplicitAny
  - strictBindCallApply
  - strictFunctionTypes
  - strictNullChecks
  - strictPropertyInitialization
  - useUnknownInCatchVariables

## 13.7 모듈

- module
  - 어떤 모듈 시스템으로 변환된 코드를 사용할지 결정하기 위해 해당 옵션을 제공합니다.
- moduleResolution
  - import에서 가져온 경로가 module에 매핑되는 과정의 로직을 지정하는데 사용할 수 있는 옵션입니다.
- CommonJS와 상호 운용성
  - 기본 내보내기 또는 네임스페이스 내보내기의 모듈 형식 간의 상호 운용성을 개선하는 몇 가지 컴파일러 옵션을 제공합니다.
    - esModuleInterop
    - allowSyntheticDefaultImports
- isolatedModules
  - 한번에 하나의 파일에서만 작동하는 바벨과 같은 외부 트랜스파일러는 타입 시스템 정보를 사용해 자바스크립트를 내보낼 수 없습니다. 결과적으로 타입 정보에 의존하며 자바스크립트를 내보내는 타입스크립트 구문 기능은 바벨 같은 트랜스파일러에서는 지원하지 않습니다.

## 13.8 자바스크립트

- 타입스크립트는 기본적으로 .js 또는 .jsx 확장자를 가진 파일을 무시하지만 allowJs, checkJs 컴파일러 옵션을 사용하면 자바스크립트 파일을 읽고, 컴파일하고, 제한된 기능이지만 타입 검사도 할 수 있습니다.
- allowJs
  - 자바스크립트 파일에 선언된 구문을 타입스크립트 파일에서 타입 검사를 하도록 허용합니다.
- checkJs
  - 타입스크립트는 단순히 자바스크립트 파일을 타입 검사할 수 있도록 타입스크립트 파일로 변환하는 것 이상을 수행할 수 있습니다.
    - allowJs 옵션이 아직 true가 아니라면 기본값을 true로 설정하기
    - .js와 .jsx 파일에서 타입 검사기 활성화하기
  - @ts-check 주석 활용하기
- JSDoc 지원
  - 자바스크립트는 타입스크립트의 풍부한 타입 구문이 없기 때문에 자바스크립트 파일에 선언된 값의 타입은 종종 타입스크립트 파일에 선언된 값만큼 명확하지 않습니다.
  - JSDoc 커뮤니티 표준이 타입을 설명할 때 주석을 사용하는 몇 가지 방법을 제공한다고 언급했습니다. allowJs와 checkJs가 활성화되면 타입스크립트는 코드의 모든 JSDoc 정의를 인식합니다.

## 13.9 구성 확장

- extends
  - 다른 tsconfig 파일에 대한 경로를 가져오고 해당 파일의 모든 설정을 복사해야 함을 나타냅니다.
- 구성 베이스
  - 처음부터 고유한 구성을 생성하거나 —init 제안을 하는 대신, 특정 런타임 환경에 맞게 미리 만들어진 베이스 tsconfig 파일로 시작할 수 있습니다.

## 13.10 프로젝트 레퍼런스

- 대규모 프로젝트에서는 프로젝트의 서로 다른 영역에 서로 다른 구성 파일을 사용하는 것이 유용할 수 있습니다. 타입스크립트에서는 여러 개의 프로젝트를 함께 빌드하는 프로젝트 레퍼런스 시스템을 정의할 수 있습니다.
- composite
  - 파일 시스템 입력과 출력이 제약 조건을 준수함을 나타냅니다.
- references
  - 타입스크립트 프로젝트는 tsconfig에 references 설정이 있는 복합 타입스크립트 프로젝트에서 생성된 출력에 의존함을 나타낼 수 있습니다.
- 빌드 모드
  - tsc 프로젝트 빌드 코디네이터 같은 것으로 향상시킵니다.

## 13.11 마치며
