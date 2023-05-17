# CHAPTER 14 구문 확장

## 14.1 클래스 매개변수 속성

- 클래스를 많이 사용하는 프로젝트이거나 클래스 이점을 갖는 프레임워크가 아니라면 클래스 매개변수 속성을 사용하지 않는 것이 좋다.

타입스크립트는 **매개변수 속성**을 선언하기 위한 단축 구문을 제공한다.

단축구문 적용전

```tsx
class Engineer {
    readonly area: string;
    constructor(area: string) {
        this.area = area;
    }

}
new Engineer("computer").area; // string
```

단축구문 적용후

```tsx
class Engineer {
    constructor(readonly area: string) {
        this.area = area;
    }

}
new Engineer("computer").area; // string
```

readonly 자리에 public, protected, private제한자 중 하나를 배치하면 타입스크립트가 동일한 이름과 타입의 속성도 선언하도록 지시한다.

매개변수 속성은 생성자의 맨 처음에 할당된다(또는 상위 클래스가 있는경우 super()를 호출한 이후 할당됨)

## 14.2 실험적인 데코레이터

ECMA스크립트 버전이 데코레이터 구문을 승인할때까지 가능하면 데코레이터를 사용하지 않는것이좋다.

## 14.3 열거형

자주 반복되는 리터럴집합이고, 그 리터럴 집합을 공통 이름으로 설명할 수 있으며, 열거형으로 전환했을 때 훨씬 더 읽기 쉽지 않은 경우라면 열거형을 사용하면 안된다.

```tsx
enum StatusCode {
    InternalServerError = 500,
    NotFound = 404,
    Ok = 200
}

StatusCode.NotFound; // 404

let statusCode: StatusCode; 
statusCode = StatusCode.Ok; // ok
statusCode = 200 // ok
```

열거형은 타입스크립트 커뮤니티에서 한편으론 ‘자바스크립트에 새로운 런타임 구문 구조를 절대 추가하지 않는다’는 타입스크립트의 일반적인 만트라를 위반한다.

이는 preserveConstEnums와 같은 옵션에 대한 몇가지 결함을 갖는다.

다른 한편으로는 열거형은 알려진 값 집합을 명시적으로 선언하는데 유용하다. 타입스크립트와 VS Code 소스 저장소에서 모두 광범위하게 사용되고있다.

### 14.3.1 자동 숫잣값

열거형의 멤버는 명시적 초깃값을 갖지 않는다면 첫번째 값을 0, 이후의 값을 +1씩 증가시킨다.

열거형 멤버의 값이 고유하고 이름과 연결되는 것 외에는 중요하지 않다면 타입스크립트에서 열거형 멤버의 값을 선택하도록 하는 것은 좋은 옵션이다.

숫잣값이 있는 열거형에서 명시적 값을 갖지 않는 모든 멤버는 이전 값보다 1 더 큰 값을 갖는다.

```tsx
enum Direction {
    Top = 1,
    Right, // 2
    Bottom, // 3
    Left, // 4
}
```

### 14.3.2 문자열값을 갖는 열거형

맴버의 값으로 문자열을 갖을 수있다.

단점으로는 숫잣값처럼 자동계산이 불가능하다는 점이다.

```tsx
enum Wat {
    FirstString = "first",
    SomeNumber = 9000,
    ImplicitNumber, // 9001
    AnotherString = "anoter",
    NotAllowed, // error: Enum member must have initializer.(1061)
}
```

→ 숫자형과 문자형 멤버를 모두 갖는 enum은 지양하자.

### 14.3.3 const 열거형

열거형은 컴파일시 런타임 객체를 생성하므로 리터럴 값 유니언을 사용하는 일반적인 전략보다 더 많은 코드를 생성한다.

**const 제한자 적용전 컴파일 전 vs 후**

```tsx
enum Wat {
    FirstString = "first",
    SomeNumber = 9000,
}
let displayValue = Wat.FirstString
```

```jsx
"use strict";
var Wat;
(function (Wat) {
    Wat["FirstString"] = "first";
    Wat[Wat["SomeNumber"] = 9000] = "SomeNumber";
})(Wat || (Wat = {}));
let displayValue = Wat.FirstString;
```

**const 제한자 적용후 컴파일 전 vs 후**

```tsx
const enum Wat {
    FirstString = "first",
    SomeNumber = 9000,
}
let displayValue = Wat.FirstString
```

```jsx
"use strict";
let displayValue = "first" /* Wat.FirstString */;
```

const 제한자를 적용한 후에도 이전처럼 객체정의를 생성하는것이 바람직한 프로젝트라면 preserveConstEnums 컴파일 옵션을 활성화시키면 이전과같이 객체정의를 생성한다.

## 14.4 네임스페이스

ECMA스크립트 모듈이 승인되기 전에는 웹 애플리케이션이 출력 코드 대부분을 브라우저에 따라 로드되는 하나의 파일로 묶는 것이 일반적이였다.

하나의 파일 출력을 위해 만들어진 프로젝트에는 코드영역과 전역 변수를 구성하는 방법이 필요했다.

타입스크립트 언어는 지금은 네임스페이스라 부르는 **내부 모듈**개념을 제공했다. 

네임스페이스는 객체의 멤버로 호출할 수 있는 exported한 콘텐츠가 있는, 전역으로 사용 가능한 객체다.

namespace키워드와 {}코드 블록으로 정의한다. 네임스페이스 블록의 모든 코드는 함수 클로저 내에서 평가된다.

### 14.4.1 네임스페이스 내보내기

콘텐츠를 네임스페이스 객체의 멤버로 만들어 내보내는 낸다.

```jsx
namespace Settings {
		export const name = "My Application";
		export const version = "1,2,3"
		export function describe() {
			return `${Settings.name} at version ${Settings.version}`
		}
}
```

여러파일에 작성해도 병합된다.

```jsx
// settings/constants.ts
namespace Settings {
	export const name = "My Application";
	export const version = "1.2.3";
}

// settings/describe.ts
namespace Settings {
		export function describe() {
			return `${Settings.name} at version ${Settings.version}`
		}
}
```

단일 파일 다중파일 선언 형식 모두 런타임에서 출력되는 객체는 3개의 키가 있는 객체다.

```jsx
const Settings = {
 describe: function describe() {
			return `${Settings.name} at version ${Settings.version}`
 },
 name: "My Application",
 version: "1.2.3",
}
```

### 14.4.2 중첩된 네임스페이스

네임스페이스는 다른 네임스페이스 내에서 네임스페이스를 내보내거나 하나 이상의 마침표(.)를 사용해서 무한으로 중첩할 수 있다.

```tsx
// settings/constants.ts
namespace MyNamespace {
  export namespace SubNamespace {
    export interface Person {
      name: string;
      age: number;
    }
  }
}
```

```tsx
//index.ts
const john: MyNamespace.SubNamespace.Person = { name: "John", age: 25 };
```

중첩된 네이스페이스는 프로젝트(상위 네임스페이스)내의 주요 영역(하위 네임스페이스)을 구분하기위한 용도로 사용한다.

### 14.4.3 타입 정의에서 네임스페이스

타입스크립트는 모듈 타입 정의에 exort as namespace와 그 뒤에 전역 이름을 포함하고, 해당 이름을 사용해서 모듈을 전역으로 사용할 수 있음을 나타낸다.

```tsx
// node_modules/@types/my-example-lib/index.d.ts
export const value: number;
export as namespace libExample;
```

```tsx
// src/index.ts
import * libExample from "my-example-lib"; // ok
const value = window.libExample.value // ok
```

### 14.4.4 네임스페이스보다 모듈을 선호함

네임스페이스대신 ECMA스크립트 모듈을 사용해 최신 표준에 맞게 사용할 수 있다.

```tsx
// settings/constants.ts
	export const name = "My Application";
	export const version = "1.2.3";

// settings/describe.ts
import { name, version} from "./constants"
		export function describe() {
			return `${name} at version ${version}`
		}
```

네임스페이스는 ECMA스크립트 모듈처럼 파일 간에 명시적으로 선언된게 아니라 암시적으로 연결되어 있기 때문에 웹팩과 같은 최신 빌더에서 사용하지 않는 파일을 제거하는것이 쉽지 않다.

따라서 ECMA스크립트 모듈을 사용해 런타임 코드를 작성하는 것이 훨씬 더 좋다.

## 14.5 타입 전용 가져오기와 내보내기

아래 코드에서 index.ts가 index.js로 컴파일될때 타입스크립트의 트랜스파일러는 ActivistArea를 제거해야 함을 알고 있다.

```tsx
// index.ts 
const value = 1;
type ActivistArea = "nature" | "people";
export { action, ActivistArea };
```

한 번에 하나의 파일에서 작동하는 바벨 같은 트랜스파일러는 각 이름이 타입 시스템에서만 사용되는지 여부를 알 수 있는 타입스크립트 타입 시스템에 접근 할 수 없다. 

isolatedModules컴파일러 옵션은 코드가 타입스크립트가 아닌 다른 도구에서 변환되는지 확인할 때 유용하다.

일부 개발자는 이를 구분하기위해 타입 가져오기를 선호하기도 한다.

```tsx
import { ClassOne , type ClassTwo } from "my-example-types";
```