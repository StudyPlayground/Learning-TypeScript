# 7. 인터페이스
연관된 이름으로 객체 형태를 설명하는 객체타입방식외의 또다른 방법이다.

객체타입과 비슷하지만 1. 더 읽기 쉬운 오류메세지, 2. 더 빠른 컴파일기능, 3. 클래스와의 더 나은 상호 운용성을 위해 좀더 선호된다.

## 7.1 타입 별칭 vs 인터페이스

```tsx
type T1 = {
	v1: number;
};

interface T2 {
	v2:number;
}
```

**차이점**

1. 인터페이스는 속성증가를 위해 병합할 수 있다. 이는 내장된 전역 인터페이스나 npm 패키지와 같은 외부 코드를 사용할 때 특히 유용하다.(아래 7.4.1참고)
2. 인터페이스는 클래스가 선언된 구조의 타입을 확인하는데 사용할 수 있지만 타입별칭을 사용할 수 없다.
3. 타입스크립트의 타입 검사기가 인터페이스에서 더 빨리 동작한다.
4. 인터페이스는 **이름 없는 객체 리터럴**의 별칭이아닌 이름이 있는 객체로 간주됨으로 어려운 특이케이스에서 나타나는 오류 메세지를 좀더 쉽게 읽을 수 있다.

→ 타입 별칭의 유니언 타입과 같은 기능이 필요할 때까지는 인터페이스를 사용하자.

## 7.2 속성 타입

### 7.2.1 선택적 속성

객체 타입과 마찬가지로 모든 객체가 필수적으로 인터페이스 속성을 가질 필요는 없다.

```tsx
interface Book {
	author:? string;
	pages: number;
}

const ok: Book = {
	author: "Rita Dove" // ok
}
```

### 7.2.2 읽기 전용 속성

속성 이름 앞에 readonly 키워드를 추가하여 읽기 전용으로 설정할 수 있다.

```tsx
interface Book {
  readonly page: number;
}

const myBook: Book  = {page:1 }
myBook.page = 2; // error: Cannot assign to 'page' because it is a read-only property.(2540)
```

readonly 제한자는 타입 시스템에서만 존재하며 인터페이스에서만 사용할 수 있다. 또한 컴파일된 자바스크립트 출력 코드에는 존재하지 않는다. 

→ readonly는 타입스크립트 타입 검사기를 사용해 개발 중에 그 속성이 수정되지 못하도록 보호하는 역할을 한다.

### 7.2.3 함수와 메서드

자바스크립트에서 객체의 속성이 함수가 될 수 있듯이 인터페이스에서도 속성으로 함수타입을 선언할 수 있는 두 가지 방법이 존재한다.

1. 메서드 구문: member(): void
2. 속성 구문: member: () ⇒ void

```tsx
interface HasBothFunctionTypes {
	property: () => string;
	method(): string;
}

const hasBoth: HasBothFunctionTypes = {
	property: () => "", 
	method() {
		return ""
	}
}

hasBoth.property(); // ok
hasBoth.method(); // ok
```

**메서드 구문과 속성 구문의 차이점**

1. readonly는 메서드구문에서만 선언할 수 있다.
2. 인터페이스 병합을 처리하는 방식이 다르다.(아래7.4.1 참고)
3. 타입처리 방식이 부분적으로 다르다 (15장 “타입 운영”에서 자세히..)

→ 타입스크립트의 향후 버전에서 메서드 구문과 속성 구문에대해 더 엄격한 옵션이 추가 될 수 있다.

**현 시점에서 추천 스타일 가이드**

- this를 사용할 상황이 있다면 메서드구문을 사용한다 (일반적으로 클래스의 인스턴스에서 사용될때)
- 반대의경우 속성구문사용

### 7.2.4 호출 시그니처

호출 시그니처란, 함수의 호출방식에대한 타입 시스템의 **설명**이다.(함수 위에 마우스 커서를 올리면 나오는 파라미터와 리턴 타입정보)

인터페이스와 객체타입은 모두 호출시그니처로 선언할 수 있다.

```tsx
type CallSignatureByObjectType = {
  (input:string): number;
};

interface CallSignatureByInterface {
  (input:string): number;
}

let hasCallCountByType: CallSignatureByObjectType = (input) => input.length; // ok
let hasCallCountByInterface: CallSignatureByInterface = (input) => input.length; // ok
```

호출 시그니처는 사용자 정의 속성을 추가로 갖는 함수를 설명하는데 사용할 수 있다.

```tsx
interface FunctionWithCount { // 객체타입으로도 가능
  count: number; // count속성 추가
  ():void;
}

let hasCallCount: FunctionWithCount;

function keepsTrackOfCalls() {
  keepsTrackOfCalls.count +=1;
  console.log(`${keepsTrackOfCalls.count}`);
}

keepsTrackOfCalls.count = 0;

function DoesNotHaveCount() {};

hasCallCount = DoesNotHaveCount; 
// error: Property 'count' is missing in type '() => void'
// but required in type 'FunctionWithCount'
```

### 7.2.5 인덱스 시그니처

임의의 속성을 허용하는 객체를 선언하는 방법을 의미한다.

```tsx
interface WordCounts {
  [i: string]: number; // number타입의 값, string타입의 키를 갖는 인터페이스
}

const counts: WordCounts = {};

counts.apple = 0;
counts.banana = 1;
counts.cherry = false; // error: number타입이 아닌 값을 할당하면 에러발생
```

인덱스 시그니처는 객체가 어떤 속성에 접근하던 값을 반환해야함을 의미하기때문에 타입 안전성을 완벽하게 보장하진 않는다.

```tsx
interface TestInterface {
  [i: string]: string;
}

const obj1:TestInterface = {
  v1: "a"
}

console.log(obj1.v1.length); // ok
const temp = obj1.v2; // 타입은 string 이지만 런타임 값은 undefined
console.log(temp.length); // 타입 시스템에서는 오류가 발생하지않지만 런타임에선 오류발생
```

→ key/value 쌍을 저장하려고하는데 키를 미리 알 수 없다면 Map을 사용하는편이 더 안전하다. Map의 .get메서드는 항상 키가 존재하지 않음을 나타내기 위해 | undefined타입을 반환하기 때문이다.(9장에서 더 자세히)

**속성과 인덱스 시그니처 혼합**

인덱스 시그니처는 명시적인 속성과도 혼합하여사용할 수 있다. 대신 명시적 속성의 타입이 인덱스 시그니처에 포함되어야하고 명시적 속성은 반드시 일치해야한다.

```tsx
interface Product { // error
  //Property 'discount' of type '1000' is not assignable to 'string' index type 'boolean'
  discount: 1000;
  [i:string]: boolean;
} 

interface Product {
  discount: 1000;
  [i:string]: number;
}

const LGcomputer:Product = {
  discount: 1000,
  price: 3000,
}

const AppleComputer:Product = {
  discount: 10, // error: Type '10' is not assignable to type '1000'.(2322)
  price: 3000,
}

```

**숫자 인덱스 시그니처**

인덱스 시그니처는 키로 number타입을 사용할 수 있다

```tsx
interface TestInterface {
  [i: number]: string;
}

const obj1:TestInterface = {
  0: "a"
}
```

그러나 string타입 인덱스와 함께 사용할땐 값의 타입은 string타입이 포괄적이여야한다.

```tsx
interface MoreNarrowNumbers {
  [i:number]: string;
  [i:string]: string | undefined; // ok
}

interface MoreNarrowStrings {
  [i:number]: string | undefined; //error: 'number' index type 'string | undefined' is not assignable to 'string' index type 'string'.
  [i:string]: string;
}
```

### 7.2.6 중첩 인터페이스

인터페이스의 속성의 타입으로 인터페이스를 가질 수 있다.

```tsx
interface Setting {
  place: string;
  year: string;
}

interface Novel {
  author: {
    name: string;
  };
  setting: Setting; // ok
}
```

## 7.3 인터페이스 확장

extends 키워드를 통해 인터페이스를 확장할 수 있다.

```tsx
interface Writing {
  title: string;
}

interface Novella extends Writing {
  pages: number;
}

const myNovel : Novella = { // ok
  title: "Ethan Frome",
  pages: 100
}

const missingPages : Novella = {
  title: "Ethan Frome",  // error: Property 'pages' is missing in type '{ title: string; }' but required in type 'Novella'
}
```

### 7.3.1 재정의된 속성

확장된 인터페이스에서 기존 인터페이스의 속성을 재정의할 수 있다.

**대부분 기존 인터페이스의 속성중 유니언타입을 더 구체화하는데 사용된다.**

```tsx
interface WithNullableName {
  name: string |null;
}

interface WithNonNullableName extends WithNullableName {
  name: string; // ok
}

interface WithNumericName extends WithNullableName {
  name: number | string; // error: Interface 'WithNumericName' incorrectly extends interface 'WithNullableName'.
//   Types of property 'name' are incompatible.
//   Type 'string | number' is not assignable to type 'string | null'.
//   Type 'number' is not assignable to type 'string'.
//   number | string 타입은 string | null에 할당 할 수 없다.
}
```

### 7.3.2 다중 인터페이스 확장

여러 개의 다른 인터페이스를 확장하여 사용할 수 있다.

```tsx
interface GivsNumber {
  giveNumber(): number;
}

interface GivsString {
  givString(): string;
}

interface GivesBothAndEither extends GivsNumber,GivsString {
  giveEither(): number | string;
}
```

## 7.4 인터페이스 병합

인터페이스가 동일한 이름으로 동일한 스코프에 선언된 경우, 선언된 모든 필드를 포함하는 더 큰 인터페이스가 코드에 추가된다.

```tsx
interface Merged {
  fromFirst:string;
}

interface Merged {
  fromSecond:number;
}

// 다음과 같음.
// interface Merged {
//  fromFirst: string;
// fromSecond: number;
// }
```

인터페이스가 여러곳에서 선언되면 코드를 이해하기 어려워지기때문에 가능하면 병합을 사용하지 않는것이 좋다.

하지만 외부 패키지, Window 같은 내장된 전역 인터페이스를 보강하는데 유용하게 사용된다.

```tsx
interface Window {
  myinvironmetVariable: string;
}

window.myinvironmetVariable // 타입 : string;
```

### 7.4.1 이름이 충돌되는 멤버

병합된 인터페이스는 타입이 동일한 이름의 속성을 여러번 선언할 수 없다.(이전 선언에 사용된 동일한 타입으로 선언은 가능함.)

```tsx
interface I1 {
  same: (input:string) => string;
  different: (input: string) => string;
}

interface I1 {
  same: (input:string) => string; // ok
  different: (input: string) => number; // error :Subsequent property declarations must have the same type. 
	// Property 'different' must be of type '(input: string) => string', 
	// but here has type '(input: string) => number'.(2717)
}
```

그러나 동일한 이름과 다른 시그니처를 가진 메서드는 정의할 수있다. → 함수 오버로드 발생

```tsx
interface I1 {
  different(input: string): string;
}

interface I1 {
  different(input: string): number;
}
```

프로퍼티구문으로 선언할 경우 이름충돌 발생

```tsx
interface I1 {
  method(input: number): number;
  property:(input: number) => number;
}

interface I1 {
  method(input: string): number; // 오버로드 발생
  property:(input: string) => number; // error: Subsequent property declarations must have the same type.
  // Property 'property' must be of type '(input: number) => number',
	// but here has type '(input: string) => number'.
}
```