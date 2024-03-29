# CHAPTER 13 구성 옵션

타입스크립트는 어떤 것이든 구성 가능하며 모든 일반적인 자바스크립트 사용패턴에 맞출 수 있다. 

타입스크립트의 구성력은 100개 이상의 풍부한 구성 옵션에서부터 비롯된다.

- tsc에 전달된 명령줄 플래그
- 타입스크립트 구성 파일 TSConfig

## 13.1 tsc 옵션

`tsc` 는 ts 파일을 컴파일하기 위한 명령어이다. tsc 명령은 타입스크립트의 대부분 옵션을 —플래그로 사용할 수 있다. 

- watch 모드

-w —watch 모드 사용 하면 종료하는대신 타입스크립트를 무기한 실행상태로 유지하고 모든 오류의 실시간 목록을 가져와 터미널을 지속적으로 업데이트한다. 

### TSConfig파일

모든 파일 이름과 구성 옵션을 항상 tsc에 제공하는 대신 대부분의 구성 옵션을 디렉터리의 tsconfig.json 파일에 구체적으로 명시할 수 있다. 

**tsconfig.json의 존재는 해당 디렉터리가 타입스크립트 프로젝트의 루트임을 나타낸다. 디렉터리에서 tsc를 실행하면 해당 tsconfig.json 파일의 모든 구성 옵션을 읽는다.** 

tsc 명령에 tsconfig.json 파일이 있는 디렉터리 경로 또는 tsc가 tsconfig.json 대신 사용할 파일 경로를 -p 또는 -project 플래그에 전달한다. 

```tsx
tsc -p path/to/tsconfig.json
```

가능하다면 타입스크립트 프로젝트에서 TSConfig 파일을 사용하기를 권장한다. VS Code 같은 IDE는 IntelliSense 기능을 제공할 때 TSConfig의 구성을 따른다. 

### tsc —init

tsconfgin 파일을 생성하기 위한 명령어

처음 몇 번의 타입스크립트 프로젝트에서는 구성 파일을 생성하기 위해 tsc —init을 사용하는 것을 권장한다. 기본값은 대부분의 프로젝트에 적용가능하고, 문서의 주석은 구성 옵션을 이해하는 데 매우 우용하다.

### CLI vs 구성

tsc —init에 따라 생성된 TSConfig 파일을 살펴보면 해당 파일의 구성 옵션이 ‘compiler Options’ 객체 내에 있다.ㄴ CLI와 TSConfig 파일에서 사용가능한 대부분의 옵션은 다음 두 가지 범주 중 하나로 분류된다.

- 컴파일러 : 포함된 각 파일이 타입스크립트에 따라 컴파일되거나 타입을 확인하는 방법
- 파일: 타입스크립트가 실행될 파일과 실행되지 않은 파일

프로젝트 레퍼런스와 같은 설정은 TSConfig 파일내에서만 사용할 수 있다. 

## 13.3 파일 포함

기본적으로 tsc는 현재 디렉터리와 하위 디렉터리에 있는 숨겨지지 않은 모든 .ts 파일에서 실행되고 숨겨진 디렉터리와 node_modules 디렉터리는 무시한다. 타입스크립트 구성은 실행할 파일 목록을 수정할 수 있다.

### 13.3.1 include

파일을 포함하는 가장 흔한 방법으로 tsconfig.json의 최상위 include 속성을 사용한다. include 속성에 타입스크립트 컴파일에 포함할 디렉터리와 파일을 설명하는 문자열 배열을 명시한다. 

### 13.3.2 exclude

프로젝트의 include파일 목록에 타입스크립트로 컴파일할 수 없는 파일이 포함되는 ㄱ영우는 exclude를 사용한다. 

```tsx
{
	"exclude" : ['**/external', 'node_modules'],
	"include" : ["src"]
}
```

기본적으로 exclude에는 커파일된 외부 라이브러리 파일에 대해 타입스크립트 컴파일러가 실행되지 않도록 node_modules, bower_components, jspm_packages

- 
    
    **bower**
    
    클라이언트 웹 개발을 위한 오픈 소스 패키지 관리 도구입니다
    
    jquery ,bootstrap과 같은 라이브러리를 관리, 설치 할 수 있습니다.
    
    Node.js로 작성된 명령행 도구를 이용해 라이브러리나 프레임워크를 간편하게 설치, 업데이트, 관리르 할 수 있습니다
    

exclude는 include의 시작 목록에서 파일을 제거하는 작업만 수행한다. 타입스크립트는 비록 가져온 파일이 exclude 목록에 명시적으로 나열되어 있더라도 포함된 파일에 따라 가져온 모든 파일에서 실행된다. 

### 13.4 대체 확장자

타입스크립트는 기본적으로 .ts인 모든 파일을 읽는다. 그러나 일부 프로젝트는 json 모듈 또는 react와 같은 ui ㄹ이브러리를 위한 jsx 구문처럼 확장자가 다른 파일을 읽을 수 있어야 한다.

jsx 구문

파일에서 jsx 구문을 사용하기 위해서는 

- 구성 옵션에서 **“jsx” 컴파일러 옵션을 활성화**한다.
- **.tsx 확장자로 파일의 이름을 지정**한다.

### jsx

타입스크립트가 .tsx 파일에 대한 자바스크립트 코드를 내보내는 방법은 jsx 컴파일러 옵션에 사용되는 값으로 결정된다. 

프로젝트는 3가지 값 중 하나를 사용한다.

| 값 | 입력 코드 | 출력 코드 | 출력 파일 확장자 |
| --- | --- | --- | --- |
| “preserve” | <div />  | <div /> | .jsx |
| “react” | <div />  | React.createElement(”div”) | .js |
| “react-native” | <div /> | <div /> | .js |

jsx에 대한 값은 tsc CLI 또는 TSConfig 파일에 제공한다.

```tsx
{
	"compilerOptions" : {
		"jsx" : "preserve"
	}
}
```

바벨과 같은 별도의 도구로 코드를 변환하는 것 처럼 타입스크립트의 내장된 트렌스파일러를 직접적으로 사용하지 않는 경우에는 jsx에 대해 허용된 값을 사용할 수 있다. Next.js또는 Remix와 같은 최신 프레임워크로 구축된 웹앱은 리액트 구성 및 컴파일 구문을 처리한다. 이러한 프레임워크 중 하나를 사용하면 타입스크립트의 내장 트랜스파일러를 직접 구성할 필요가 없다.

### .tsx 파일의 제네릭 화살표 함수

10장 제네릭에서 **제네릭 화살표 함수의 구문이 jsx 구문과 충돌**한다고 언급했다. .tsx 파일에서 화살표 함수에 대한 타입 인수 <T>를 작성하려고 하면 T요소의 시작 태그에 대한 종료 태그가 없기 때문에 구문 오류가 발생한다.

```tsx
const identity = <T>(input : T) => input;
// Error: JSX element T has no corresponding closing tag
```

**이러한 구문 모호성을 해결하기 위해 타입 인수에 unknwon 제약 조건을 추가할 수 있다**. 타입 인수 기본값은 **unknown 타입이므로 코드 동작이 전혀 변경되지 않는다.** 단지 JSX 요소가 아닌 타입 인수를 읽도록 타입스크립트에 지시한다. 

```tsx
const identity = <T = unkwown>(input : T) => input;
```

### resolveJsonModule

**타입스크립트는 resolveJsonModule 컴파일러 옵션을 true로 설정하면 .json 파일을 읽을 수 있다.** 이렇게 하면 .json 파일을 마치 객체를 내보내는 .ts 파일인 것 처럼 가져오고 해당 객체의 타입을 const 변수인 것처럼 유추한다. ⇒ 이거 저절로 되는거 아니엇군아

객체가 포함된 JSON 파일이라면 구조 분해 할당을 사용한다. activist.json 파일에 “activist” 문자열을 정의하고 usesActivist.ts 파일에서 구조분해 가져오기를 사용해 “activist”를 가져온다.

```tsx
"resolveJsonModule": true,
```

esModuleInterop 컴파일러 옵션이 활성화된 경우에도 기본 가져오기를 사용할 수 있다.

```tsx
import data from ".activist.json";
```

array 또는 number같은 다른 리터럴 타입을 포함한 json파일이라면 imponrt 구문으로 *를 사용한다. activist.json 파일에 문자열 배열을 정의한 다음 useActivist.ts 파일에서부터 가져온다.

```tsx
import * as activists from './activists.json';
```

### 13.5 자바스크립트로 내보내기

바벨 같은 전용 컴파일러 도구의 등장으로 일부 프로젝트에서는 타입스크립트의 역할이 타입 검사만으로 축소되었지만 타입스크립트 구문을 자바스크립트로 컴파일하기 위해 여전히 타입스크립트에 의존하고 있는 프로젝트도 많다. 프로젝트가 타입스크립트에 단일의존성을 가지고, tsc 명령을 사용해 자바스크립트를 출력하는 작업은 유용하다.

### 13.5.1 outDir

기본적으로 타입스크립트는 출력 파일을 해당 소스 파일과 동일한 위치에 생성한다. 

경우에 따라 출력 파일을 다른 폴더에 생성하는 것이 더 나을 수 있다. 예를 들어 프로젝트를 dist또는 lib에 저장하는게 더 좋을 수도 있다.

**타입스크립트의 outDir 컴파일러 옵션을 사용하면 출력 파일의 루트 디렉터리를 다르게 지정할 수 있다. 출력 파일은 입력 파일과 동일한 디렉터리 구조를 유지한다.**

타입스크립트는 모든 입력 파일에서 가장 긴 공통 하위 경로를 찾아 출력 파일을 저장할 루트 디렉터리를 계산한다. **즉, 모든 입력 소스 파일을 단일 디렉터리에 배치하는 프로젝트는 해당 디렉터리를 루트로 처리한다.**

### 13.5.2 target

타입스크립트는 ES3과 같은 오래된 환경에서 실행할 수 있는 자바스크립트 출력 파일을 생성할 수 있다. 또한 대부분의 환경은 자바스클비트의 최신 구문을 지원한다.

타입스크립트는 자바스크립트 코드 구문을 지원하기 위해 어느 버전까지 변환해야 하는지를 지정하는 target 컴파일러 옵션을 제공한다. target을 지정하지 않으면 이전버전과의 호환성을 이용해 기본적으로 es3을 제공한다(ㄴ0ㄱ) tsc —init은 기본으로 es2016을 지정하도록 설정되어 있지만 대상 플랫폼에 따라 가능한 한 최신 자바스크립트 구문을 사용하는 것이 좋다. 오래된 환경에서 최신자바스크립트 기능을 지원하려면 더 많은 자바스크립트를 생성해야 하므로, 파일 크기가 조금 더 커지고 런타임 성능이 저하된다.

<aside>
💡 target을 적어도 es2019 이상으로 지정하지 않을 이유가 없음

</aside>

### 13.5.3 내보내기 선언

선언 파일에서 .d.ts 선언 파일을 패키지로 배포해 사용하는 곳에서 코드 타입을 표시한다. 대부분의 패키지는 타입스크립트의 declaration 컴파일ㄹ 옵션을 사용해 소스 파일에서 .d.ts 출력 파일을 내보낸다. 

```tsx
tsc --declaration
```

```tsx
{
	"compilerOptions" : {
		"declaration" : true
	}
}
```

.d.ts 출력 파일은 outDir 옵션에 따라 .js 파일과 동일한 출력 규칙에 따라 내보내진다.

### emitDeclarationOnly

declaration 컴파일러 옵션에 대한 특별 추가로 타입스크립트가 .js와 .jsx 파일 없이 선언 파일만 내보내도록 하는 컴파일러 옵션이다. 

이는 외부 도구를 사용해 출력 자바스크립트를 생성하지만 여전히 타입스크립트를 사용해 출력 선언 파일을 생성하려는 프로젝트에 유용하다.

이 옵션이 활성화된 경우 이번 장의 후반부에서 살펴볼 declaration또는 composite 컴파일러옵션을 활성화해야 한다.

### 15.5.4 소스 맵

소스맵은 출력 파일의 내용이 원본 소스 파일과 어떻게 일치하는지에 대한 설명이다.

소스 맵은 출력 파일을 탐색할 때 디버거 같은 개발자 도구에서 원본 소스 코드를 표시하도록 설정한다.

브라우저 개발자 도구와 IDE에서 디버깅하는 동안 원본 소스 파일 내용을 볼 수 있게 하는 시각적 디버거에 소스맵이 유용하다.

타입스크립트는 출력 파일과 함께 소스 맵을 출력하는 기능도 제공한다.

### sourceMap

타입스크립트의 sourceMap 컴파일러 옵션을 사용하면, .js 또는 .jsx 출력 파일과 함께 .js.map 또는 .jsx.map 소스 맵을 출력할 수 있다. 

그렇지 않으면 소스맵 파일에 해당 출력 자바스크립트 파일과 동일한 이름으로 동일 디렉터리에 생성된다. 

### declarationMap

타입스크립트는 .d.ts 선언 파일에 대한 소스 맵을 생성할 수도 있다. declarationMap 컴파일러 옵션은 원본 소스 파일에 해당하는 각 .d.ts에 대한 .d.ts.map 소스 맵을 생성하도록 지시한다. declarationMap을 활성화하면 VS Code 같은 IDE에서 원본 소스 파일로 이동할 수 있다.

<aside>
💡 프로젝트 레퍼런스를 작업할 때 유용하다고 한다.

</aside>

### 13.5.5 noEmit

다른 도구를 이용해 소스 파일을 컴파일하고, 자바스클비트를 출력하는 프로젝트에서 타입스크립트는 파일 생성을 모두 건너뒤도록 지시할 수 있다. noEmit 컴파일러 옵션을 활성화하면 타입스크립트가 파일 생성을 모두 건너뛰도록 할 수 있다. 이렇게 되면 타입스크립트가 온전한 타입 검사기로만 작동한다.(대부분 일케 되어있을듯?)

## 13.6 타입 검사

## 13.7 모듈

## 13.8 자바스크립트

## 13.9 구성 확장

## 13.10 프로젝트 레퍼런스

## 13.11 마치며
