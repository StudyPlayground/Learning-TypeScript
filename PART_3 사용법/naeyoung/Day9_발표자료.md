### npm

이 방법을 사용하기를 추천합니다. 아래는 예시입니다.

`npm install --save-dev @types/node`

`node` 를 위한 자료형(Typing)이 컴파일 과정에 자동으로 포함될 겁니다. 모듈을 사용하지 않는 경우 `types` 참조를 추가해 주어야 할 수 있습니다.

`/// <reference types="node" />`

npm 의 "foo" 패키지에 대응되는 자료형 패키지는 "@types/foo" 입니다. 원하시는 패키지를 찾을 수 없는 경우, [타입서치(TypeSearch)](https://microsoft.github.io/TypeSearch/) 사이트에서 한 번 찾아보세요.

그래도 찾을 수 없는 경우, 찾고 있는 패키지가 자료형(Typing)을 [함께 제공](https://www.typescriptlang.org/docs/handbook/declaration-files/publishing.html)하고 있지는 않은지 확인해보세요. 이 경우 주로 `package.json` 파일의 `"types"` 나 `"typings"` 필드(Field)를 통해 제공되지만, `/// <reference path="" />` 같은 주석을 사용하여 패키지 안의 ".d.ts" 파일들을 직접 가져와야 할 수도 있습니다.

# 타입 수정하기

## 1. DefinitelyTyped fork 하기

## 2. 브랜치 만들기

브랜치를 생성하고, 그 후, 터미널에서 `npm install`을 실행한다.

## 3. 타입 수정하기

이 프로젝트의 본 목적인 타입을 수정한다. 패키지의 버전이 엄청 다르지 않는 이상, 고칠 부분은 그다지 많지 않다. 초심자가 오픈소스에 기여하기 가장 좋은 프로젝트중 하나일 수도 있다.

## 4. 수정한 타입에 대한 테스트 코드 추가/수정하기

테스트를 추가하고, 버전을 교체한다. 만약 만약 브레이킹 체인지(Breaking change)를 만들었다면, [메이저 버전(major version)](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/README.ko.md#%ED%8C%A8%ED%82%A4%EC%A7%80%EB%A5%BC-%EC%83%88-%EB%A9%94%EC%9D%B4%EC%A0%80-%EB%B2%84%EC%A0%84major-version%EC%97%90-%EB%A7%9E%EA%B2%8C-%EA%B0%B1%EC%8B%A0%ED%95%98%EA%B3%A0-%EC%8B%B6%EC%96%B4%EC%9A%94)을 올린다. 

패키지 머릿주석의 "Definitions by" 부분에 작성자의 이름을 추가할 수 있다. 

- 이름을 추가하면 다른 사람들이 그 패키지에 대한 풀 리퀘스트(Pull request)나 이슈(Issue)를 만들 때 알람이 간다.
- `// Definitions by: Alice <https://github.com/alice>, Bob <https://github.com/bob>` 와 같이 여러분의 이름을 줄의 맨 마지막에 추가할 수 있다.
- 사람이 너무 많을 경우엔, 다음과 같이 여러 줄로 쓸 수도 있다.
- `[npm test <package to test>` 명령을 실행시키고 결과를 확인해주세요](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/README.ko.md#%EA%B2%80%EC%A6%9D%ED%95%98%EA%B8%B0).
- 테스트를 통과했으면 PR을 날린다.

## 5. Pull Request 요청

`DefinitelyTyped`에 Pull Request 을 날린다. 템플릿에 맞게 작성하면 된다. 이미 존재하는 패키지에 대한 풀 리퀘스트(Pull request)를 만들었을 경우에는, `dt-bot` 이 이전 저자들을 자동으로 호출하는지 확인. 그렇지 않은 경우에는, 여러분이 직접 풀 리퀘스트(Pull request)와 관계있는 사람들을 호출할 수도 있다. 

https://github.com/DefinitelyTyped/DefinitelyTyped/pull/65414

예시)

https://github.com/DefinitelyTyped/DefinitelyTyped/pull/65393

https://github.com/DefinitelyTyped/DefinitelyTyped/pull/65329/files

# 새 패키지 만들기

만약 라이브러리를 만드는 중이고 라이브러리가 타입스크립트(TypeScript)로 쓰여있다면, Definitely Typed 에 선언(Declaration)을 올리는 대신 패키지에 [자동생성된 선언(Declaration) 파일을 포함](https://www.typescriptlang.org/docs/handbook/declaration-files/publishing.html)시킬 수 있다. 

npm 패키지를 위한 자료형(Typing) 패키지를 만드시려면, 패키지의 이름과 같은 이름의 디렉토리를 만들어주세요. npm 에 올라가 있지 않은 패키지를 위한 자료형(Typing) 패키지를 만드시려면, 그 패키지가 npm 에 올라와 있는 패키지와 이름이 겹치지 않는지 확인해주세요. (`npm info <my-package>` 명령어를 사용하여 `<my-package>` 패키지가 npm 에 있는지 확인할 수 있습니다.)

새 자료형 패키지는 다음과 같은 구조로 구성되어있어야만 합니다.

| 파일 이름 | 용도 |
| --- | --- |
| index.d.ts | 패키지를 위한 자료형(Typing)을 포함하는 파일 |
| https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/README.ko.md#my-package-teststs | 자료형(Typing)의 테스트를 위한 파일 
이 파일의 코드는 실행되지는 않지만, 자료형 검사(Type checking)를 통과해야 한다 |
| https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/README.ko.md#tsconfigjson | tsc 명령을 돌릴 수 있게 해주는 파일 |
| https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/README.ko.md#linter-tslintjson | 린터(Linter)를 사용할 수 있게 해주는 파일 |

이 파일들은, npm ≥ 5.2.0 에서는 `npx dts-gen --dt --name <my-package> --template module` 명령으로, 그 이하 경우에는 `npm install -g dts-gen` 와 `dts-gen --dt --name <my-package> --template module` 명령으로 만들 수 있다. 

[npm: re](https://www.npmjs.com/package/re)

https://github.com/DefinitelyTyped/DefinitelyTyped/pull/65304/files

[DefinitelyTyped/README.ko.md at master · DefinitelyTyped/DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/README.ko.md)

[[DefinitelyTyped] 내가 쓰고있는 패키지의 타입 고치기!](https://seohyun0120.tistory.com/entry/DefinitelyTyped-내가-쓰고있는-패키지의-타입-고치기)
