# CHAPTER 6 배열

- 자바스크립트에서는 배열 내부에 모든 타입의 값을 혼합해서 저장할 수 있지만, 타입스크립트에서는 원하는 타입만을 저장 할 수 있게 제한할 수 있고, 해당 타입에 맞는 동작만 실행시킬수 있게 제한할 수 있다.
- 초기 배열에 담긴 요소를 통해 배열의 타입을 유추할 수 있다.(변수의 초기값을통해 변수의 타입을 유추하듯)

```tsx
const array = [1,2]; // string[]
array.push("문자열입니다.") // error :Argument of type 'string' is not assignable to parameter of type 'number'.
```

## 6.1 배열 타입

배열의 타입 애너테이션은 배열의 요소 타입 다음에 []를 사용한다.

```tsx
let array : number[]
array = [1,3,4]
```

### 6.1.1 배열과 함수 타입

다음 f1과 f2의 타입은 동일하지 않음.

```tsx
let f1: () => string[] // string 배열을 반환하는 함수타입
let f2: (()=>string)[] // string을 반환하는 함수의 배열타입
```

### 6.1.2 유니언 타입 배열

괄호의 위치에따라 의미하는 바가 달라진다.

```tsx
let v1: string | number[]; // string 타입 이거나 number의 배열 타입
let v2: (string | number)[]; // number 이거나 string인 요소를 갖는 배열 타입
```

### 6.1.3 any 배열의 진화

타입 애너테이션이 없는 빈 배열은 잠재적으로 잘못된 값 추가를 허용해 타입스크립트의 타입 검사기가 갖는 이점을 부분적으로 무력화한다.

```tsx
let array = []; // 타입 : any
array.push(""); // 타입 : string[]
array[0] = 0; // 타입 : (string | number)[]
```

### 6.1.4 다차원 배열

2차원 배열 또는 배열의 배열은 두 개 의[]를 갖는다.

```tsx
let arrays : number[][]; // (number[])[]로 표현가능

arrays  = [
  [1,2,3],
  [4,5,6],
  [7,8,9]
]
```

## 6.2 배열 멤버

타입스크립트는 배열의 맴버의 타입을 파악해 알려준다.

```tsx
const array = [1,2,3];
const v1 = array[0] // v1은 number타입이다
```

유니언 타입으로된 배열의 멤버는 그 자체로 동일한 유니언 타입으로 알려준다.

```tsx
const array = [1,2,3,4,"a","b"];
const v1 = array[0] // v1은  string| number 타입이다
```

### 6.2.1 주의사항: 불안정한 멤버

타입스크립트의 타입 시스템은 기술적으로 불안정하다. 대부분 올바른 타입을 얻을 수 있지만 아래와같은 예시에선 타입시스템의 이해가 올바르지 않을 수 도있는데 

```tsx
const array = [1,2,3]
array[9999]  
// 자바스크립트에선 undefined를 반환한다
// 타입스크립트 컴파일러 기본 설정에서 오류를 발생하지 않고 여전히 number타입으로 인지한다.
// noUncheckedIndexedAccess플래그가 있지만 이 플래그는 매우 엄격해서 대부분의 프로젝트에서 사용하지 않는다.
```

따라서 타입스크립트는 검색된 배열의 멤버가 존재하는지 의도적으로 확인하지 않음을 알 수 있다.(옵션을통해 지긴한다.)

## 6.3 스프레드와 나머지 매개변수

나머지매개변수 … 과 배열 스프레드는 자바스크립트에서 배열과 상호작용하는 핵심 방법이다.

타입스크립트는 두가지방법을 모두 이해한다.

### 6.3.1 스프레드

스프레드 연산자를 통해 배열을 결합하면 결합된 결과의 배열의 타입은 합쳐진 배열의 유니언 타입으로 이해된다.(동일하면 하나의 타입으로 이해)

```tsx
const array1 = [1,2,3];
const array2 = [4,5,6];
const array3 = ["a","b","c"];

const joined1 = [...array1,...array2]; // number[]
const joined2 = [...array1,...array3]; // (string | number)[]
```

### 6.3.2 나머지 매개변수 스프레드

타입스크립트는 나머지 매개변수에 인자를 전달할때 배열을 스프레드하는 자바스크립트 실행을 인식하고 이에 대해 타입 검사를 수행 한다 또한 나머지 매개변수의 인수로 사용되는 배열은 나머지 매개변수와 동일한 배열 타입을 가져야한다.

```tsx
function f1 (param1: string, ...param2: string[]) {
// ...
}

const array1 = ["a","b","c"];
const array2 = [1,2,3];

f1("aa",...array1); // ok
 // 인수로 사용되는 array2의 타입이 나머지 매개변수param2와 타입이 일치하지 않기때문에 에러 발생
 // error : Argument of type 'number' is not assignable to parameter of type 'string'
f1("aa",...array2); 

```

## 6.4 튜플

- 자바스크립트 배열은 이론상 어떤 크기라도 될 수 있다. 하지만 때로는 튜플이라고 하는 **고정된 크기의 배열**을 사용하는 것이 유용할때도 있다.
- 튜플 배열은 각 인덱스마다 특정 타입을 가지며 유니언 타입보다 더 구체적이다.

```tsx
let array : [number,string]; // 튜플타입 선언
array = [1,"string"];
array = [false,"string"]; // error: Type 'boolean' is not assignable to type 'number'.(2322)
array = [1000];
 // error: Type '[number]' is not assignable to type '[number, string]'.
 // Source has 1 element(s) but target requires 2.
```

- 튜플과 배열 구조분해 할당

```tsx
let [v1,v2] = Math.random() > 0.5 ? [1,"a"] : [2,"b"];
// v1: number, v2: string
```

### 6.4.1 튜플 할당 가능성

- 가변 길이의 배열 타입은 튜플 타입에 할당할 수 없다.

```tsx
// array1배열 내부에 [number,string]이 존재하는걸 알 수 있지만, 
// 타입스크립트는 더 일반적인 (number | string)[]으로 타입을 유추한다(가변길이의 배열로 유추)
const array1 = [1,"a"] 

const array2:[number,string] = array1 // error : 
//Type '(string | number)[]' is not assignable to type '[number, string]'.
//Target requires 2 element(s) but source may have fewer.
```

- array3배열은 2개의 멤버만 가져야함으로, 멤버타입이 동일하게 시작하더라도 array1을 할당할때 에러가 발생한다

```tsx

const array1: [boolean,number,string] = [false,1,"a"];
const array2: [boolean,number] = [false,1];
const array3: [boolean,number] = array1; // error:
	//  Type '[boolean, number, string]' is not assignable to type '[boolean, number]'.
	//  Source has 3 element(s) but target allows only 2.
```

- 나머지 매개변수로서의 튜플
    - 튜플이 아닌 타입의 값을 인수로 전달하면 타입이 일치하지 않을 가능성이 있기때문에 에러를 발생한다.
        
        ```tsx
        function f1 (p1: string,p2: number) {
        // ...
        }
        
        const v = ["a",1]; // 튜플이 아닌 (string | number)[]타입.
        f1(...v); // error: A spread argument must either have a tuple type or be passed to a rest parameter
        ```
        
    - 튜플은 타입을 보장해준다.
        
        ```tsx
        function f1 (p1: string,...p2:[string|number]) {
        // ...
        }
        
        const v: [string,number] = ["a",1]; // 튜플 타입
        f1(...v); // ok
        ```
        

### 6.4.2 튜플 추론

타입스크립트는 생성된 배열을 튜플이 아닌 가변 길이의 배열로 취급한다. 따라서 아래 예시중 f1이 반환하는 값의 타입은 [string,number]가 아닌 (string | number)[]타입으로 추론된다.

```tsx
function f1 (p1: string) {
  //..
  return [p1[0],p1.length];
}

const [v1,v2] = f1("example"); // v1타입: (string | number), v2타입: (string | number)
```

타입스크립트에서 값이 일반적인 배열이 아닌 튜플타입이여야함을 나타내는 방법은 다음 두가지로 나뉜다.

1. **명시적 튜플 타입**
    1. 함수에 대한 반환 타입 애너테이션처럼 튜플 타입도 타입 애너테이션에 사용할 수 있다. 이렇게되면 반환시 사용되는 배열 리터럴이 가변 길이의 배열이 아닌 튜플 타입으로 간주된다.
        
        ```tsx
        function f1 (p1:string): [string,number] {
          return [p1[0],p1.length]
        }
        
        const [v1,v2] = f1("example"); // v1타입: string, v2타입: number
        ```
        
2. **const 어서션**

명시적으로 튜플타입을 입력하는 작업은 코드가 수정됬을때 필요시 수정이 필요하기때문에 번거로울 수있다.

배열 리터럴 뒤에 “as const”를 추가하면 배열이 튜플로 처리되어야함을 나타낸다. 추가로 const 어서션은 튜플로 전환하는것을 넘어 해당 튜플이 읽기전용으로 값이 수정될 수 없을을 나타낸다.

```tsx
const array1:[number,string] = [1,"a"]; // 명시적 튜플(수정가능)
array1[0] = 100; // ok

const array2: [number,string] = [2,"b"] as const; // error: The type 'readonly [2, "b"]' is 'readonly' and cannot be assigned to the mutable type '[number, string]'.

const array3 = [3,"c"] as const;
array3[0] = 4; // error: Cannot assign to '0' because it is a read-only property.(2540)
```

읽기전용 튜플은 함수반환시 사용되면 함수 호출부에서 사용하기편리하다.

```tsx
function f1(p1: string) {
  return [p1[0],p1.length] as const
}

// v1타입이 (string | number) 가아닌 string
// v2타입이 (string | number) 가아닌 number
const [v1,v2] = f1("example"); 
```