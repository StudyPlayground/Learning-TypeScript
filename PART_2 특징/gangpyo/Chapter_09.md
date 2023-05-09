# CHAPTER 9 타입 제한자

더 정확한 타입작성

## 9.1 top 타입

top타입은 시스템에서 가능한 모든 값을 나타내는 타입이다.

다른 모든 타입 값은 타입이 top인 위치에 제공될 수 있다. 즉 모든 타입은 top 타입에 할당할 수 있다.

### 9.1.1 any 다시 보기

any는 모든 타입에 제공될 수 있다는점에서 top 타입처럼 동작한다.

```tsx
let anyValue: any;
anyValue = "a";
anyValue = 123;
```

any타입은 타입검사를 수행하지 않도록 명시적으로 지시한다는 문제를 갖는다. 이는 타입스크립트의 타입 검사를 건너뛰려고 할 때 유용하지만 타입스크립트의 유용성이 줄어듦을 의미하게된다.

어떤 값이든 될 수 있음을 나타내려면 **unknown 타입**이 훨씬 안전하다.

### 9.1.2 unknown

unknown타입이 진정한 top 타입이다. any와 비슷하지만 주요 차이점은 타입스크립트는 unknown 타입의 값을 다음과같이 훨씬 더 제한적으로 취급한다.

1. 타입스크립트는 unknown타입 값의 속성에 직접 접근할 수 없다.
    
    ```
    function f1 (q1:unknown) {
        console.log(q1.toUpperCase()) // error
    }
    ```
    
2. top타입이 아닌 타입에는 할당할 수 없다.
    
    ```
    function f1 (q1:unknown) {
        let anyValue : any;
        anyValue = q1; // ok
        
        let numberValue = 1;
        numberValue = q1; // error : Type 'unknown' is not assignable to type 'number'.(2322)
    }
    ```
    

타입스크립트가 unknown 타입에 접근할 수 있는 유일한 방법은 instanceof, typeof, 타입 어서션을 사용하는 것처럼 **타입을 제한 하는 경우**이다.

```
function f1 (q1:unknown) {
    if(typeof q1 === "string"){
    console.log(q1.toUpperCase()) // ok
    }
}
```

→ any보다 unknown이 안전하다. 

## 9.2 타입 서술어

타입스크립트가 인수가 특정 타입인지 여부를 나타내기위해 boolean값을 반환하는 함수를 위한 특별한 구문이다.

‘사용자 정의 타입 가이드’ 라고도 부른다.

 instanceof, typeof와 유사한 자체 타입 가드.

매개변수로 전달된 인수가 매개변수의 타입보다 더 구체적인 타입인지 여부를 나타내는데 사용.

```tsx
function typePredicate(input: WidType): input is NarrowType;
```

## 9.3 타입 연산자

키워드나 기존 타입의 이름만을 사용하여 모든 타입을 나타낼 수는 없다.

### 9.3.1 keyof

자바스크립트 객체는 일반적으로 string타입인 동적값을 사용하여 검색된 멤버를 찾는다. 이런 포괄적인 원시타입을 사용하게되면 아래의 “email”처럼 유효하지않는 키가 허용된다.

```tsx
interface Person {
    firstName: string;
    lastName: string;
}

function getPersonInfo(person : Person,key: string){
	return person[key] // tsconfig에의해 error를 발생시킬 수 있다.
}

const me = {firstName:"noh",lastName: "gang pyo"};
getPersonInfo(me,"firstName");
getPersonInfo(me,"email"); // 허용되지만 사용하면 안된다.
```

이를 유니언 타입으로 해결할 수 있긴하다.

```tsx
interface Person {
    firstName: string;
    lastName: string;
}

function getPersonInfo(person : Person,key: "firstName" |"lastName"){
	return person[key] 
}

const me = {firstName:"noh",lastName: "gang pyo"};
getPersonInfo(me,"firstName");
getPersonInfo(me,"email"); // error
```

근데 키가많아질경우 일일이 작성하는건 번거롭다.

**keyof연산자**를통해 해결(존재하는 타입의 키를 바탕으로 유니언 타입을 생성)

```tsx
interface Person {
    firstName: string;
    lastName: string;
}

function getPersonInfo(person : Person,key: keyof Person){
	return person[key] 
}

const me = {firstName:"noh",lastName: "gang pyo"};
getPersonInfo(me,"firstName");
getPersonInfo(me,"email"); // error
```

### 9.3.2 typeof

제공되는 값의 타입을 반환한다. 값의 타입을 수동으로 작성하는것이 복잡할때 사용하면 유용하다.

```tsx
const obj1 = {
    a: 1,
    b: "str",
}

let obj2 : typeof obj1; 

// obj2의 타입은
// {
//		a: number;
//		b: string;
//	}
```

자바스크립트의 typeof연산자와 이름은 같지만 기능은 다르다.

자바스크립트의 typeof연산자는 타입에대한 문자열 이름을 반환하는 런타임 연산이다.

타입스크립트의 typeof연산자는 타입스크립트에서만 사용할 수 있으며 컴파일된 자바스크립트 코드에 나타나지 않는다.

**keyof typeof**

명시적 인터페이스 타입이 없는 객체에 허용된 키를 나타내는 타입을 관리하는 수고를 줄일 수 있다.

```tsx
const me = {
    firstName: "Noh",
    lastName: "gang pyo"
}

function getInfo(key: keyof typeof me){
	return me[key] 
}

getInfo("firstName");
getInfo("email"); // error
```

## 9.4 타입 어서션

타입스크립트는 값의 타입에 대한 타입 시스템의 이해를 재정의 하기위한 구문으로 타입 어서션을 제공한다.

```tsx
const rawData = '["apple","orange"]';

JSON.parse(rawData)  // 타입 : any
JSON.parse(rawData) as string[] // 타입 : string[]
JSON.parse(rawData) as [string,string]// 타입 : [string,string]
JSON.parse(rawData) as ["grace","frankie"] // 타입: ["grace","frankie"]
```

자바스크립트로 컴파일되면 

```jsx
const rawData = '["apple","orange"]';

JSON.parse(rawData) // 타입: any
JSON.parse(rawData) // 타입: string[]
JSON.parse(rawData) // 타입: [string,string]
JSON.parse(rawData) // 타입: ["grace","frankie"]
```

→ 타입스크립트 모범 사례는 가능한한 타입 어서션을 사용하지 않는 것이다. 코드가 완전히 타입화되고 어서션을 사용해 타입스크립트의 타입 이해를 방해할 필요가 없는 것이 가장 좋다.

### 9.4.1 포착된 오류 타입 어서션

오류를 처리할때 어서션이 유용할 수 있다. 일반적으로 try블록의 코드가 오류를 발생했을때 catch블록에서 포착된 오류가 어떤 타입인지 아는것은 일반적으로 불가능하다.

에러의 타입을 확신한다면 어서션을 오류로 처리할 수 있다.

```tsx
try {
    // 오류발생
} catch(error) {
    console.warn("oH, no!",(error as Error).message);
}
```

예상된 오류인지를 판단하기위해 instanceof검사와같은 타입 내로잉을 사용하는것이 더 안전하다.

```jsx
try {
    // 오류발생
} catch(error) {
    console.warn("oH, no!",error instanceof Error ? error.message : error);
}
```

### 9.4.2 non-null 어서션

실제로는 아니고 이론상(?) null 또는 undefined를 포함할 수 있는 변수에서 null 과 undefined를 제거할때 사용.

```tsx
let maybeDate = Math.random() > 0.5 ? undefined : new Date();
maybeDate as Date; // 타입이 Date 라고 간주됨
maybeDate!; // 타입이 Date 라고 간주됨
```

Map.get API에서 유용하다.

아래의 person는 “firstName”키를 포함하고 있으므로 !사용해  | undefined를 제거할 수 있다.

```jsx
const person = new Map([
    ["firstName" , "noh"],
    ["age" , "27"],
])

const v1 = person.get("firstName"); // v1타입은 string | undefined
const v2 = person.get("firstName")!; // v2타입은 string
```

### 9.4.3 타입 어서션 주의 사항

any타입과 마찬가지로 타입 어서션은 타입스크립트의 타입 시스템에 필요한 하나의 도피 수단이다. 따라서 꼭 필요한 경우가 아니라면 사용하면 안된다.

**어서션 vs 선언**

타입 애너테이션을 사용하거나 타입스크립트가 초깃값에서 변수의 타입을 유추하도록 하는 것이 바람직하다.

**어서션 할당 가능성**

완전히 서로 관련이 없는 타입 사이에 타입 어서션이 있는경우 오류를 감지한다.

```tsx
let v1 = "a" as number; // error 
// Conversion of type 'string' to type 'number' may be a mistake
// because neither type sufficiently overlaps with the other. 
// If this was intentional, convert the expression to 'unknown' first.
```

## 9.5 const 어서션

const 어서션은 배열, 원시 타입, 값, 별칭등 모든 값을 상수로 취급해야함을 나타내는데 사용한다.

- 배열은 가변 배열이 아닌 읽기전용 튜플로 취급
- 리터럴은 일반적인 원시타입과 동등하지 않고 리터럴로 취급
- 객체의 속성은 읽기 전용으로 간주

### 9.5.1 리터럴에서 원시 타입으로

```tsx
const getName = () => "Maria"; // 타입: () => string
const getNameConst = () => "Maria" as const; // 타입: () => "Maria"
```

```
interface Person {
    name: string;
    gender : "male" | "female";
}

function printePersonGender(person : Person) {
    if(person.gender === "male") {
        console.log("male");
    }else {
        console.log("female");
    }
}

const me = {
    name : "gangpyo Noh",
    gender : "male" as const,
}

const you = {
    name : "??",
    gender : "male",
}

// me의 타입: { name: string, gender: "male" }
printePersonGender(me);

// you의 타입: { name: string, gender: string }
printePersonGender(you); // error 
```

### 9.5.2 읽기 전용 객체

객체리터럴은 let 변수의 초깃값의 타입이 확장되는것과 동일한 방식으로 속성타입을 확장한다.

```tsx
let v1 = 'a' // v1의 타입은 string타입으로 확장됨.

// obj의속성들의 타입은 각각 string,number로 확장됨.
const obj = {
    v1: "a",
    v2: 1,
}
```

그러나 as const를 사용해 값 리터럴을 어서션하면 유추된 타입이 가능한한 구체적으로 전환된다.

모든 멤버 속성은 readonly가 되고, 리터럴은 일반적인 원시 타입대신 고유한 리터럴타입으로 간주되며, 배열은 읽기 전용 튜플이 된다.

즉, 모든 멤버에 동일한 const어서션 로직이 재귀적으로 적용됨.

```tsx
const obj = {
    v1: "a",
    v2: 1,
} as const

// const obj: {
//   readonly v1: "a";
//   readonly v2: 1;
// }
```