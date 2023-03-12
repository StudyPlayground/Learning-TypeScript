# CHAPTER 4 객체
## 4.1 객체 타입

- poet변수의 타입은 number타입인 born과 string타입인 name으로 이루어진 두 개의 속성을 갖는 객체.

```jsx
const poet = {
    born : 1935,
    name: "Mary Oliver"
}

poet.born // 타입 : string
poet["name"] // 타입 : number
```

### 4.1.2 객체 타입 선언

- 타입을 기존 객체에서 직접 유추하는 방법도 좋지만, 명시적으로 선언 할 수도 있음.
- 객체 타입은 객체 리터럴과 유사하게 보이지만 필드값 대신 타입을 사용해 설명함.

```jsx
let foo : {
	born: number;
	name: string;
}

foo = {
	born : 1934,
	name : "Mary",
};

foo = "test" // error : Type "string" is not assignable to type '{born : number, name: string;}'
```

### 4.1.2 별칭 객체 타입

- 객체를 사용할때 일일이 객체타입을 지정해주는건 번거러움
- 별칭 객체타입을 할당하는것이 일반적
- 할당 가능성 오류 메세지를 좀더 읽기 쉽게 만들어준다.

```jsx
type Poet = {
	born: number,
	name: string,
}

let poetLater : Poet;

poetLater = {
	born: 1900,
	name: "test"
}

poetLater = "Emily"; // error : Type "string" is not to assignable to "Poet"
```

## 4.2 구조적 타이핑

- 타입스크립트의 타입 시스템은 **구조적으로 타입화**되어 있음
    - 타입을 충족하는 모든 값을 해당 타입의 값으로 사용할 수 있음

```jsx
type WithFirstName = {
    firstName: string;
}

type WithLastName = {
    lastName : string;
}

const hasBoth = {
    firstName : "Lucille",
    lastName : "Clifton"
}

let withFirstName : WithFirstName = hasBoth; // OK: hasBoth는 string 타입인 firstName을 포함함
let withLastName: WithLastName = hasBoth; // OK: hasBoth는 string 타입인 lastName을 포함함
```

- 덕 타이핑과는 다름
    - 타입스크립트 타입 검사기에서 구조적 타이핑은 정석 시스템이 타입을 검사하는 경우를 말함
    - 덕 타이핑(동적타이핑의 한 종류)은 런타임에서 사용될 때까지 객체 타입을 검사하지 않는 것을 말함
    
    → 자바스크립트는 덕타입, 타입스크립트는 구조적으로 타입화됨
    

### 4.2.1 사용 검사

- 객체 타입으로 애너테이션된 위치에 값을 제공할때 타입스크립트는 값을 해당 객체 타입에 할당할 수 있는지 확인한다
- 할당하려는 객체에 객체타입의 멤버가 없다면 타입오류를 발생시킨다

```jsx
type WithFirstName = {
    firstName: string;
}

const name: WithFirstName = {
	last : "aa" // error : Property "last" is missing in type "{firstName : string;}"
} 
```

- 객체의 속성이 일치하지 않아도 타입오류를 발생시킨다

```jsx
type WithFirstName = {
    firstName: string;
}

const name: WithFirstName = {
	firstName: 123 // error : Type 'number' is not assignable to type "string"
} 
```

### 4.2.2 초과 속성 검사

- 변수가 객체 타입으로 선언되고, 초깃값에 객체 타입에서 정의된 것보다 많은 필드가 있다면 타입스크립트에서 타입 오류가 발생한다
- 따라서 변수를 객체 타입으로 선언하는것은 타입 검사기가 해당 타입에 예상되는 필드만 있는지 확인하는 방법이기도 하다

```jsx
type Poet = {
    born : number;
    name:string;
}

const extraProperty : Poet = {
    born : 1023,
    name: "Ma",
    activity: "walking" // error: Type '{ born: number; name: string; activity: string; }' is not assignable to type 'Poet'.
												//  Object literal may only specify known properties, and 'activity' does not exist in type 'Poet'.(2322)
}
```

- 초과 속성 검사는 객체 타입으로 선언된 위치에서 생성되는 **객체 리터럴**에 대해서만 발생한다

```jsx
type Poet = {
    born : number;
    name:string;
} 

const existingObject = {
    activity: "walking",
    born: 1999,
    name: "Mary"
}

 // OK : extraPropertyButOk변수의 초깃값이 구조적으로 Poet와 일치하기 때문에 에러가 발생하지 않는다.
const extraPropertyButOk: Poet = existingObject;
```

- 타입스크립트에서 초과 속성을 금지하면 코드를 깨끗하게 유지할 수 있고 예상하는데로 동작하도록 만들 수 있음

### 4.2.3 중첩된 객체 타입

- 자바스크립트 객체는 다른 객체의 멤버로 중첩될수있으므로 타입스크립트 객체 타입도 중첩된 객체타입을 나타낼수 있어야한다
- 중첩된 객체타입을 고유한 타입으로 바꿔서 사용하면 코드와 오류메세지를 더 읽기 쉬워진다.(에러 메세지에 타입이 명시되기 때문)

### 4.2.4 선택적 속성

- 타입의 속성 애너테이션에서 : 앞에  ?를 추가하면 선택적 속성임을 나타냄

```jsx
type  Book = {
    author?: string;
    pages : number;
}

const ok: Book = {
    author : "Rita",
    pages : 20
}

const ok2 :Book  = {
    pages : 20
}
```

- 선택적 속성과 undefined를 포함한 유니언 타입의 속성 사이에는 차이가 있다는걸 명심하자.
    - 필수로 선언된 속성과의 | undefined는 그 값이 undefined일지라도 반드시 존재해야함.

```jsx
type Writers = {
    author: string| undefined;
    editor?: string;
}

const hasRequired: Writers = {
    author: undefined,
}

const missingRequired : Writers = {} // error 
```

## 4.3 객체 타입 유니언

## 4.3.1 유추된 객체 타입 유니언

- 변수에 여러 객체 타입 중 하나가 될 수 있는 초깃값이 주어지면 타입스크립트는 해당 변수의타입을 객체 타입 유니언으로 유추.

```jsx
const poem = Math.random() > 0.5  
    ? {name: "th",pages: 9}
    : {name: "aa", rhymes :true};

// 타입 : 
// {
//    name: string;
//    pages: number;
//    rhymes?: undefined;
// } 
// | 
// {
//    name: string;
//    pages?: undefined;
//    rhymes: boolean;
// }
```

### 4.3.2 명시된 객체 타입 유니언

```jsx
type PoemWithPages = {
    name: string;
    pages : number;
}

type PoemWithRhymes = {
    name: string;
    rhymes: boolean;
}

type Poem = PoemWithPages | PoemWithRhymes // name을 항상 갖는 유니언타입으로 명시됨.

const poem : Poem = Math.random() > 0.5  
    ? {name: "th",pages: 9}
    : {name: "aa", rhymes :true};

poem.name // OK
poem.pages // error: Prooerty "pages" does not exist on type "Poem"
```

- 잠재적으로 존재하지 않는 멤버에 접근은 제한된다.

### 4.3.3 객체 타입 내로잉

- pages가 타입 가드 역할을해 PoemWithPages타입임을 나타내는지 확인한다

```jsx
type PoemWithPages = {
    name: string;
    pages : number;
}

type PoemWithRhymes = {
    name: string;
    rhymes: boolean;
}

type Poem = PoemWithPages | PoemWithRhymes 

const poem : Poem = Math.random() > 0.5  
    ? {name: "th",pages: 9}
    : {name: "aa", rhymes :true};

if("pages" in poem) {
    poem.pages; // OK
} else {
	poem.rhymes; // OK
}
```

- 타입스크립트는 if(poem.pages)와 같은 형식으로 참여부를 확인하는것을 허용하지 않음

```jsx
if(poem.pages) {...} // error 
```

### 4.3.4 판별된 유니언

- 객체의 속성이 객체의 형태를 나타내도록 함
- 객체의 타입을 가리키는 속성을 판별값으로 여김

```jsx
type PoemWithPages = {
    name: string;
    pages: number;
		type: "pages";  // 판별값
}

type PoemWithRhymes = {
    name: string;
    rhymes: boolean;
		type: "rhymes"; // 판별값
}

type Poem = PoemWithPages | PoemWithRhymes 

const poem : Poem = Math.random() > 0.5  
    ? {name: "th",pages: 9}
    : {name: "aa", rhymes :true};

if(poem.type === "pages") {
    poem.pages; // OK
} else {
	poem.rhymes; // OK
}
poem.type // 타입 : "pages" | "rhymes"
poem.pages // error  : 내로잉 없이는 값에 존재하는 속성을 보장할 수 없다.
```

### 4.4 교차 타입

- & 을 활용해 여러타입을 동시에 나타냄

```jsx
type Artwork = {
    genre : string;
    name : string;
}

type Writing = {
    pages : number;
    name : string;
}

type WrittenArt = Artwork & Writing;
// {
//  genre : string;
//  name : string;
//  pages : number;
// }
```

- 하나의 타입으로 판별된 유니언 타입을 설명하는데 유용

```jsx
type ShortPoem = { author: string } & (
    | { kigo: string; type : "haiku"; } 
    | { meter: number; type : "villanelle"; }
);
```

### 4.4.1 교차 타입의 위험성

- 작성자나 타입스크립트를 혼동시키는 방식으로 사용되기 쉽기때문에 코드를 간결하게 유지해야한다
- 긴 할당 가능성 오류
    - 코드가 간결하지않으면 할당 가능성 오류 메세지를 읽기가 어려워지진다
- never타입
    - 교차 타입은 잘못 사용되기 쉽고 불가능한 타입을 생성한다
    
    ```jsx
    type NotPossible = number & string; // 타입 : never
    ```
    
    - never타입은 프로그래밍 언어에서 bottom타입 혹은 empty타입을 뜻한다
        - 타입을 가질 수 없고 참조할 수 없는 타입을 의미
    
    ```jsx
    let notNumber: NotPossible = 0; // error
    let notString: never = ""; // error
    ```
    
    - 대부분의 타입스크립트 프로젝트는 never타입을 거의 사용하지 않지만 코드에서 불가능한 상태를 나타내기위해 가끔 등장. 하지만 거의 대부분 교차 타입을 잘못 사용해 발생할 실수일 가능성이 높다