# CHAPTER 3 유니언과 리터럴

유니언: 값에 허용된 타입을 두개 이상의 가능한 타입으로 확장하는것

내로잉: 값에 허용된 타입이 하나 이상의 가능한 타입이 되지 않도록 좁히는것

→ 다른 프로그래밍 언어에서는 불가능한 타입스크립트에선 가능한 “코드 정보에 입각한 추론”

## 3.1 유니언 타입

- “이거 또는 저거” 와 같은 타입
- | (수직선) 연산자를 통해 유니언 타입을 나타냄

### 3.1.1 유니언 타입 선언

- 변수의 초깃값이 있더라도 변수에 대한 명시적 타입 애너테이션을 제공하는 것이 유용할때 사용.

```jsx
// 초깃값은 null이지만 잠재적으로 string타입이 할당될 수있음을 알려줌
let foo : string | null = null;  

if(Math.random() > 0.5) {
	foo = "test"
}
```

### 3.1.2 유니언 속성

- 값이 유니언 타입일때 타입스크립트는 유니언으로 선언한 모든 타입을 충족하는 속성에만 접근할 수 있음.

```jsx
let foo = Math.random() >0.5 ? "haha" : 123; 

foo.toString(); // OK
foo.toUpperCase(); // error : number타입에는 toUpperCase가 존재하지 않기때문
foo.toFixed(); // error : string타입에는 toFixed가 존재하지 않기때문
```

## 3.2 내로잉

- 값이 정의,선언 혹은 이전에 유추된 것보다 더 구체적인 타입임을 ***코드***에서 유추하는것을 의미.
- 타입스크립트가 값의 타입이 이전에 알려진 것보다 더 좁혀졌다는 것을 알게 되면 더 구체적인 타입으로 취급.
- 타입을 좁힐수 있는 논리적 검사를 **타입 가드**라고함.
- 흔한 타입가드 두가지
    - 값 할당을 통한 내로잉
    - 조건 검사를 통한 내로잉

### 3.2.1 값 할당을 통한 내로잉

- 변수에 값을 직접 할당하면 타입스크립트는 변수의 타입을 할당된 값의 타입으로 내로잉.

```jsx
let foo : number | string;

foo = "test"; // 타입스크립트는 foo변수를 string으로 인식하게됨.
foo.toUpperCase(); // OK
foo.toFixed(); // error : number타입에 해당하는 함수이기때문에.
```

### 3.2.2 조건 검사를 통한 내로잉

- if문을통해 변수의 타입을 내로잉

```jsx
// 변수의 값을통한 내로잉

let foo  = Math.random() > 0.5 ? "test" : 123;

if(foo === "test") {
    foo.toUpperCase(); // OK 
}
    foo.toUpperCase(); // error 
```

```jsx
// typeof 연산자를 활용한 내로잉

let foo  = Math.random() > 0.5 ? "test" : 123;

if(typeof foo === "string" ) {
    foo.toUpperCase(); // OK
}
    foo.toUpperCase(); // error
```

## 3.3 리터럴 타입

- 좀더 구체적인 버전의 원시 타입
- 원시 타입 값중 어떤 것이 아닌 **특정 원싯값**으로 알려진 타입

```jsx
// foo는 어떤 타입일까? 
// string타입일까? x 
// "test"타입!
const foo  = "test";
```

- 모든 원시타입에 존재
    - boolean : true | false
    - null (null 자체)
    - undefined (undefined자체)
    - number : 0 | 1 | 2 | 3…. (무수히 많음)
    - string : “” | “a” | “b”… (무수히 많음)

### 3.3.1 리터럴 할당 가능성

- 0 과 1처럼  동일한 원시 타입일지라도 서로 다른 리터럴 타입은 서로 할당이 불가능

```jsx
let foo : 0;
foo = 0; // OK
foo = 1; // error
```

- 그러나 리터럴 타입에 해당하는 원시타입에는 할당 가능

```jsx
let bar : number;
bar = 0; // OK
bar = 1; // OK
```

## 3.4 엄격한 null 검사

- 타입스크립트는 “십억 달러의 실수”를 바로잡기위해 엄격한 null 검사를 사용하며 이는 최신 프로그래밍 언어의 큰 변화 중 하나.

### 3.4.1 십억 달러의 실수

- 1965년 , null 참조의 발명으로 수많은 오류, 취약성및 시스템 충돌이 지난 40년동안 십억달러의 고통과 피해를 입었음을 의미.
- 엄격한 null 검사가 없은 언어에서는 타입이 지정된 변수에 null을 할당하는 것이 허용됨.

```jsx
const foo : string = null;
```

- 타입스크립트 컴파일러는 실행방식에대한 옵션으로 strictNullChecks를 제공.
    - strictNullChecks : true
        
        ```jsx
        let foo = Math.random() > 0.5 ? "123" : undefined;
        
        foo.toLocaleLowerCase() // error : 'foo' is possibly 'undefined'
        ```
        
    - strictNullChecks : false
        
        ```jsx
        let foo = Math.random() > 0.5 ? "123" : undefined;
        
        foo.toLocaleLowerCase() // 오류가 검출되진 않으나, 런타임에러가 발생할 수 있다.
        ```
        
- 타입스크립트의 모범 사례는 일반적으로 strictNullChecks를 true로 설정

### 3.4.2 참 검사를 통한 내로잉

- 자바스크립트에서 false, 0,-0,0n,””,null,undefined,NaN처럼 falsy로 정의된 값을 제외한 모든 값은 참.

```jsx
// undefined는 항상 falsy이므로 
// if문의 코드 블록에서는 foo가 string타입이 되어야 한다고 추론할 수 있음.
let foo = Math.random() > 0.5 ? "test" : undefined;

if(foo) {
    foo.toUpperCase(); // OK
}

foo.toUpperCase(); // error : "foo" is possibly "undefined"
```

- 참 여부 확인 외에 다른 기능은 제공하지 않음
    - string | undefined 값에 대해 알고 있는 것이 falsy라면(아래에선 else문), 그값이 빈 문자열이여서 falsy인지, undefined여서 falsy인지는 알 수 없음
        
        ```tsx
        let foo = Math.random() > 0.5 ? "test" : undefined;
        
        if(foo) {
            foo.toUpperCase(); // 타입 : string
        } else {
            foo // 타입 : undefined | string(빈문자열인경우)
        }
        ```
        

### 3.4.3 초깃값이 없는 변수

- 자바스크립트에서 초깃값이 없는 변수는 기본적으로 undefined.
- undefined를 포함하지 않는 타입으로 변수를 선언한 다음 값을 할당하기 전에 사용할때 에러 발생

```jsx
let foo : string;
foo?.length; // error : "foo" is used before being assigned.
```

- 변수 타입에 undefined를 포함시키면 에러가 발생하지 않음
    - 사용전에 정의할 필요가 없음을 의미

```jsx
let foo : string | undefined;
foo?.length; // OK
```

### 3.5 타입 별칭

- 재사용하는 타입에 더 쉬운 이름을 할당하는 방식

```jsx
type RawData = boolean | number | string| null | undefined'
let rawDataFirst :RawData;
```

### 3.5.1 타입 별칭은 자바스크립트가 아닙니다.

- 타입 별칭은 타입 애너테이션처럼 자바스크립트로 컴파일되지 않음
- 순전히 타입 시스템에만 존재하므로 런타임 코드에서는 참조할 수 없음
    
    ```jsx
    type SomeType = string | undefined
    console.log(SomeType) // error 
    ```
    
- 순전히 “개발 시”에만 존재함.

### 3.5.2 타입 별칭 결합

- 타입 별칭은 다른 타입별칭을 참조하 수있음

```jsx
type Id = number | string;
type IdMaybe  = Id | string| undefined | null; // number | undefined | null 과 같음.
```

- 사용 순서대로 타입 별칭을 선언할 필요는 없음

```jsx
type IdMaybe  = Id | string| undefined | null;
type Id = number | string;
```