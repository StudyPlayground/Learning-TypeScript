# CHAPTER 11 선언 파일
타입스크립트는 전역 변수와 API와같은 환경에 특화된 기능의 타입 형태를 알려주는 방법이 필요하다.

예를들어 Node.js에서 실행되는 프로젝트는 브라우저에서 사용 할 수 없는 내장 Node.js모듈에 젭근할 수 있으며, 그 반대도 마찬가지다.

타입스크립트는 별도로 타입 형태를 선언할 수 있는데, 타입 선언은 파일 이름이 .d.ts 확장자로 끝나는 **선언 파일**에 작성된다.

## 11.1 선언파일

.d.ts 선언 파일은 런타임 코드를 포함할 수 없다는 제약사항 말고는 .ts파일과 유사하게 동작한다.

선언 파일은 다른 타입스크립트 파일과 마찬가지로 임포트해서 사용할 수 있다.

```tsx
// types.d.ts
export interface Character {
  catchphrase?: string;
  name: string;
}

// index.ts
import { Character } from "./types";

export const character: Character = {
  catchphrase: "Yee",
  name: "Sandy Cheeks"
}
```

→ 선언파일은 값이 아닌 타입만 선언할 수 있는 코드 영역을 의미하는 **앰비언트 컨텍스트**를 생성한다

## 11.2 런타임 값 선언

함수나 변수같은 런타임 값을 생성하지는 않지만 declare키워드를 통해 존재를 알릴수는 있다.

예를들어 script태그 같은 외부에서 불러오는 코드에서 선언되어있는 변수나 함수의 타입을 declare키워드를 통해 타입시스템에게 알려주는것을 의미한다.

**declare로 변수를 선언할 때**

```tsx
//types.d.ts
declare let declared: string; // ok
declare let initializer: string = "a"; // error: Initializers are not allowed in ambient contexts.(1039)
```

→ 초깃값이 허용되지 않는다는 점을 제외하고 일반적인 변수 선언과 동일한 구문을 사용한다.

**declare로 함수나 클래스의 메서드를 선언할때**

```tsx
//fairies.d.ts
declare function f1(p1:string):boolean;
declare function f2(p2:string):boolean {return true}; // error: An implementation cannot be declared in ambient contexts.

declare class Class1 {
  f1(p1:string):boolean;
  f2(p2:string):boolean {return true}; // error: An implementation cannot be declared in ambient contexts.
}
```

→ 본문작성을 허용하지 않는점을 제외하고 일반적인 함수와 메서드 선언과 동일한 구문을 사용한다.

선언 파일 외부에서도 declare키워드를 사용할 수 있다. 전역으로 사용 가능한 변수가 해당 파일에서만 사용되어야 하는경우 declare키워드가 유용하다.

```tsx
// index.ts
// 전연변수임에도 불구하고 declare키워드를 부여함으로써 index.ts에서만 사용가능하게한다.
declare const myGlobalValue: string;
console.log(myGlobalValue); // ok
```

인터페이스와 같은 타입 형태는 .d.ts선언 파일에서 declare키워드 유무와는 관계없이 허용되지만, 함수나 변수같은 런타임 구문에 declare 키워드가 없다면 타입 오류가 발생한다. 

```tsx
//index.d.ts
interface Wirter {}
declare interface Writer {}

declare const fullName : string;
declare const firstName : "Liz";

const lastName = "Lemon"; // error
```

### 11.2.1 전역변수

import 또는 export  문이 없는 타입스크립트 파일은 모듈이 아닌 스크립트로 취급되기 때문에 여기에 선언된 타입을 포함한 구문은 전역으로 사용됩니다.

다음 globals.d.ts에 선언된 version은 version.ts파일에서 globals.d.ts를 **import를 하지 않아도** 사용할 수 있다.

```tsx
//globals.d.ts
declare const version: string;
```

```tsx
//version.ts
export function logVersion() {
	console.log(`Version: ${version}`) // ok
}
```

→ .d.ts 파일에 선언된 전역 타입에 접근이 불가능할경우, .d.ts파일이 아무것도 export하거나 import하지 않는지 확인하자.

### 11.2.2 전역 인터페이스 병합

인터페이스는 동일한 이름의 다른 인터페이스와 병합되기 때문에 import와 export문이 없는 .d.ts선언 파일 같은 전역 스크립트 컨텍스트에서 인터페이스를 선언하면 해당 인터페이스가 전역으로 확장된다.

### 11.2.3 전역 확장

.d.ts 파일에 import 또는 export 문을 항상 금지할 수 있는 것은 아니다. declare global 코드 블록 구문을 사용해 해당 블록 내용이 전역 컨텍스트에 있다고 표시할수있다.

```tsx
// types.d.ts
// (모듈 컨텍스트)
declare global {
// (전역 컨텍스트)
}
// (모듈 컨텍스트)
```

## 11.3 내장된 선언

Array, Function, Map, Set과 같은 전역 객체는 타입 시스템이 알아야 하지만 코드에서 선언되지 않는 구문이다.

이와 같은 전역 객체는 디노,Node.js, 웹 브라우저 등에서 실행되는 런타임 코드에 의해 제공된다.

### 11.3.1 라이브러리 선언

모든 자바스크립트 런타임에 존재하는 Array,Function 같은 내장 전역 객체는 lib.[target].d.ts파일 이름으로 선언된다. 여기서 target은 ES5,ES2020,ESNext와 같이 프로젝트에서 대상으로 하는 자바스크립트의 최쇠 지원 버전이다.

**라이브러리 target**

타입스크립트는 프로젝트의 tsconfig.json(기본값은 es5)에서 제공된 target설정에 따라 적절한 lib파일을 포함한다. 자바스크립트 최신 버전에 대한 연속적인 **lib파일들은 인터페이스 병합**을 사용해 서로 빌드된다.

예를들어 target이 es2016인 프로젝트는 lib.es5.d.ts, lib.es2015.d.ts 그리고 lib.es2016.d.ts까지 포함된다.

target이 es5이면, String.prototype.startsWith와 같은 ES2015 이상의 기능은 인식되지 않는다.

### 11.3.2 DOM 선언

자바스크립트 언어에대한 타입외 웹 브라우저를 위한 타입을 의미한다. lib.dom.d.ts파일과 다른 lib.*.d.ts선언 파일에도 저장된다.

lib 컴파일러 옵션을 재정의하지 않는 타입스크립트 프로젝트는 DOM타입을 기본으로 포함한다. 이 말은 Node.js와같은 브라우저가 아닌 환경에서 실행되는 프로젝트도 DOM타입에 접근해도 타입에러를 발생시키지 않는다는 의미인데, 이는 혼란을 야기시킬수 있다. 이는 tsconfig.json으로 조정이 가능하며, 이는 13장에서 살표보겠다.

## 11.4 모듈 선언

모듈의 문자열 이름 앞에 declare키워드를 사용하면 모듈의 내용을 타입 시스템에 알릴 수 있다.

```tsx
// modules.d.ts
declare module "my-example-lib" {
    export const value : string;
}

// index.ts
import {value} from "my-example-lib";
console.log(value)
```

→ 코드에서 declare module을 자주 사용하면 안된다. 이는 주로 다음 절에 나오는 와일드카드모듈 선언과 이 장의 후반부에서 다루는 패키지 타입과 함께 사용된다. 또한 타입스크립트가 .json 파일의 가져오기를 기본으로 인식하도록 설정하는 컴파일러 옵션인 resolveJsonModule에 대한 정보는 13장 ‘구성 옵션’에서 확인할 수 있다.

### 11.4.1 와일드카드 모듈 선언

모듈 선언은 자바스크립트와 타입스크립트 파일 확장자가 아닌 특정 파일의 내용을 코드로 가져올 수 있음을 웹 어플리케이션에 알리기 위해 사용한다. 모듈 선언으로 하나의 * 와일드카드를 포함해 해당 패턴과 일치하는 모든 모듈을 나타낼 수 있다. 보통 create-react-app 과 create-next-app같은곳에서 스타일을 객체로 표현할 수 있는 이유가 “*.module.css”와 같은 패턴으로 모듈을 정의하기 때문이다.

```tsx
//styles.d.ts
declare module "*.module.css" {
	const styles: { [i: string]: string};
	export defult styles;
}
```

```tsx
//component.ts
import styles from "./styles.modules.css",
styles.anyClassName; // 타입: string;
```

## 11.5 패키지 타입

타입스크립트로 작성된 프로젝트는 여전히 .js로 컴파일된 파일이 포함된 패키지를 배포한다. 일반적으로 .d.ts파일을 사용해 이러한 자바스크립트 파일뒤에 타입스크립트 타입 시스템 형태를 지원하도록 선언한다.

### 11.5.1 선언

타입스크립트는 입력된 파일에 대한 .d.ts 출력 파일과 자바스크립트 출력 파일을 함께 생성하는 선언 옵션을 제공한다.

예를들어 입력된파일을 다음이라고 가정하면

```tsx
//index.ts
export const greet = (text: string) => {
	console.log('Hello,${text}!');
}
```

module은 es2015, target은 es2015인 선언 옵션을 사용해 다음 출력 파일을 생성한다.

```tsx
//index.d.ts 
export declare const greet: (string) => void;

//index.js
export const greet = (text) => {
	console.log('Hello, ${text}!');
}
```

→ 일반적으로 .js파일을 생성하는 타입스크립트로 작성된 대부분의 패키지도 해당 파일과 함께 .d.ts를 번들로 묶는 것이 좋다. 컴파일옵션설정은 13장에서 더 자세히 알아보자

### 11.5.2 패키지 타입 의존성

타입스크립트는 node_modules 의존성 내부에서 번들로 제공되는 .d.ts파일을 감지하고 활용할 수 있다.

jest 테스트 프레임 워크를 예를들면, 이는 타입스크립트로 작성되어있으며, jest패키지 내에 .d.ts파일을 제공한다. 이는 @jest/globals 패키지에 대한 의존성을 가지며, jest를 전역으로 사용할 수 있게 해준다.

### 11.5.3 패키지 타입 노출

프로젝트가 npm에 배포되고 사용자를 위한 타입을 제공하려면 패키지의 package.json파일에 types 필드를 추가해 루트 선언 파일을 가리켜야한다. main필드와 유사하게 동작하지만 .js확장자대신 .d.t 확장자를 사용한다.

만약 package.json파일에 types필드가 없다면, 타입스크립트는 ./index.d.ts를 기본값으로 지정한다.

대부분의 패키지는 타입스크립트의 선언 컴파일러 옵션을 사용해 소스 파일로부터 .js파일과 함께 .d.ts파일을 생성한다. 자세한 내용은 13장 구성옵션에서.

## 11.6 DefinitelyTyped

모든 프로젝트가 타입스크립트로 작성된 것은 아니다. 특정 패키지를 사용하려면, 그 패키지의 타입형태를 알려줘야한다. 

이를 위해 타입스크립트 팀과 커뮤니티는 커퓨니티에서 작성된 패키지 정의를 수용하기위해 DefinitelyTyped라는 거대 저장소를 생성하고 관리중이다.

이러한 DT는 수 천개의 .d.ts정의 패키지로 구성되어있다.

DT패키지는 사용하려는 패키지이름과 동일한 이름으로 npm에 @types 범위로 게시된다.