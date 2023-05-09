# CHAPTER 10 제네릭

- 제네릭은 함수의 매개변수 타입과 반환 타입 간의 관계을 설명하는데 사용되는 도구다.
- 타입스크립트에서 함수와 같은 구조체(?)는 **제네릭 타입 매개변수**를 원하는 수만큼 선언할 수 있다.
- 이 매개변수는 구조체 내에서의 사용법에따라 타입이 결정된다.
- 타입 매개변수는 T,U같은 단일 문자이름 또는 Key와 Value 같은 파스칼 케이스 이름을 갖는다.
- 이 장에서 다루는 모든 구조체에서는 <,>를 사용해 somFunction<T>또는 SomeInterface<T>처럼 제네릭을 선언한다.

## 10.1 제네릭 함수

함수의 매개변수 괄호 바로 앞에 <,>로 묶인 타입 매개변수에 별칭을 작성함으로써 함수를 제네릭으로 만든다.

다음 f1함수는 input매개변수에 대한 타입 매개변수 T를 선언한다. 그러면 타입스크립트는 f1함수의 반환타입이 T임을 유추한다.

```tsx
function f1 <T>(input:T) {
	return input
}

const v1 = f1("a"); // v1의 타입은 "a"이다
const v2 = f1(1); // v2의 타입은 1이다
```

화살표 함수도 제네릭을 만들 수 있다.

```tsx
const f1 = <T>(input:T) => input;

const v1 = f1("a"); // v1의 타입은 string이다
const v2 = f1(1); // v1의 타입은 string이다
```

→ .tsx파일에서 JSX구문과 충돌함으로 일부 제한이 있을 수 있다. (13장 구성 옵션 참고)

### 10.1.1 명시적 제네릭 호출 타입

대부분의 타입스크립트는 함수가 호출되는 방식에따라 타입 인수를 유추한다.

예를들어 위의 f1함수는 인자로 전달되는 값의 타입을 사용해 매개변수의 타입을 유추한다.

```tsx
const f1 = <T>(callback:(input:T) => void) => {
    return (input: T) => {
        callback(input);
    }
};

const v1 = f1((p: string) => {}) // 유추가능 f1:(input:string) => void

const v2 = f1((p) => {}) // 유추불가 f1:(input:unknown) => void
```

아래와같은 변수에대한 명시적 타입에너테이션과마찬가지로, 유추가 가능하다면 굳이 중복하여 작성하지 않는다.

```tsx
let v1:string = "a"; // 중복
let v2 = "a"; // string으로 유추가능
```

### 10.1.2 다중 함수 타입 매개변수

임의의 수의 타입 매개변수를 쉼표로 구분해 함수를 정의한다.

```tsx
function makeTuple<First,Second>(first: First,second:Second) {
    return [first,second] as const
}

let tuple = makeTuple("a",true); // readonly[string,boolean] 타입
```

함수가 여러개의 타입 매개변수를 선언하면 해당 함수의 호출은 명시적으로 제네릭타입을 모두 선언하거나 모두 선언하지 않아야합니다. 타입스크립트는 아직 제네릭호출중 일부 타입만을 유추하지는 못한다.

```tsx
function makeTuple<First,Second>(first: First,second:Second) {
    return [first,second] as const
}

let tuple1 = makeTuple<string,boolean>("a",true); // ok
let tuple2 = makeTuple("a",true); // ok
let tuple3 = makeTuple<string>("a",true); // error: Expected 2 type arguments, but got 1.(2558)

```

→ 제네릭 구조체에서 두 개보다 많은 타입 매개변수는 지양하자. 런타임 함수 매개변수처럼 많이 사용할수록 코드를 읽고 이해하는 것이 점점 어려워진다.

## 10.2 제네릭 인터페이스

인터페이스도 제네릭으로 선언할 수 있다.

```tsx
interface Person<T> {
    name: T 
}

const me: Person<string> = {
    name : "gangpyo"
}

const you: Person<string> = {
    name : true // error: Type 'boolean' is not assignable to type 'string'.(2322)
}
```

타입스크립트에서 내장 Array 메서드는 제네릭 인터페이스로 정의되어있다.

```tsx
interface Array<T> {

    pop(): T | undefined;
    push(...items: T[]): number;
}
```

### 10.2.1 유추된 제네릭 인터페이스 타입

아래에서와같이 인수로전달된 값의 타입에따라 인터페이스의 타입인수의 타입을 유추할 수 있다.

```tsx
interface Person<T> {
    name: T    
    phone: T
}

function getName<A>(person: Person<A>) {}

getName(
		{
 		 name:"gangpyo", // 유추된 A타입인수: string
 		 phone:true // error: Type 'boolean' is not assignable to type 'string'.(2322)
		}
);
```

인터페이스가 타입 매개변수를 선언하는 경우, 해당 인터페이스를 참조하는 모든 타입 애너테이션은 이에 상응하는 타입 인수를 제공해야 한다.

```tsx
interface Person<T> {
    name: T    
    phone: T
}

const me : Person = {
 // Generic type 'Person<T>' requires 1 type argument(s).(2314)
}
```

## 10.3 제네릭 클래스

인터페이스처럼 클래스도 클래스내에서 사용할 임의의 수의 타입 매개변수를 선언할 수 있습니다.

```tsx
class Secret<Key,Value> {
    key: Key;
    value:Value;

    constructor(key: Key, value:Value) {
        this.key = key;
        this.value = value;
    }

    getValue(key: Key):Value | undefined {
        return this.key === key ? this.value : undefined;
    }
}
```

제네릭 인터페이스와 마찬가지로 클래스를 타입 애너테이션으로 사용할때 제네릭 타입이 무엇인지 작성해야한다.

```tsx
let testClass1 : Secret<string,number>;
let testClass2 : Secret; // error: Generic type 'Secret<Key, Value>' requires 2 type argument(s)
```

### 10.3.1 명시적 제네릭 클래스 타입

new Secret(1234,”a”)와같이 함수 생성자에 전달된 매개변수의 타입으로부터 타입 인수를 유추할 수 있다면 타입스크립트는 유추된 타입을 사용하지만, 그렇지 않은경우 기본타입은 unknown이 된다.

unknown이 되는것을 피하기 위해선 명시적으로 타입 매개변수를 제공해주면 된다.

### 10.3.2 제네릭 클래스 확장

타입스크립트는 상위 클래스에 대한 타입 인수를유추하지 않습니다.기본 값이 없는 모든 타입 인수는 명시적 타입 애너테이션을 사용해 지정해야합니다.

```tsx
class Quote<T> {
    lines:T;
    constructor(lines:T) {
        this.lines = lines;
    }
}

class SpokenQuote extends Quote { // error: Generic type 'Quote<T>' requires 1 type argument(s).
    speak() {
        console.log(this.lines.join("\n"));
    }
}
```

제네릭 하위 클래스는 자체 타입 인수를 상위 클래스에 전달할 수 있다.(타입이름은 일치하지 않아도된다)

```tsx
class Animal<T> {
    name: T;
    constructor(name: T) {
        this.name = name;
    }
}

class Dog<Value> extends Animal<Value> { // Dog하위 클래스의 자체타입 인수인 Value타입인수를 Animal에도 전달가능.
    gender: string;
    constructor(name: Value,gender: string) {
        super(name);
        this.gender = gender;
    }
}
```

### 10.3.3 제네릭 인터페이스 구현

제네릭 인터페이스는 위의 제네릭 상위 클래스를 확장하는것과같이 모든 타입 매개변수가 클래스쪽에 선언되어야한다.

```tsx
interface Dog<T> { 
    name : T;
}

// 전
class Animal implements Dog { // error: Generic type 'Dog<T>' requires 1 type argument(s).(2314)
    name: string;
    gender: string;

    constructor(name: string, gender: string) {
        this.name = name;
        this.gender = gender;
    }
}

// 후
class Animal implements Dog<string> { // ok
    name: string;
    gender: string;

    constructor(name: string, gender: string) {
        this.name = name;
        this.gender = gender;
    }
}
```

### 10.3.4 메서드 제네릭

클래스의 메서드는 클래스의 인스턴스와는 별개로 자체 제네릭 타입을 선언할 수 있고, 클래스의 타입매개변수를 사용할 수 있다.

```tsx
class createPaireFactory<Key> {
    key: Key;

    constructor(key: Key ){
        this.key = key;
    }

    createPair<Value>(value: Value) { // 별도의 Value타입 매개변수 선언
        return { key: this.key, value}; // 반환타입이 클래스의 타입 매개변수를사용하여 { key: Key,valye: Value } 로 유추된다.
    }
}
```

### 10.3.5 정적 클래스 제네릭

클래스의 정적 멤버는 인스턴스와 구별되기때문에 인스턴스에 접근할 수 없거나 타입정보를 지정할 수 없다.

따라서 정적 멤버는 자체 타입 매개변수는 선언할 수 있지만, 클래스의 타입 매개변수를 사용할 수는 없다.

```tsx
class BothLogger<OnInstance> {
    instanceLog(value: OnInstance) {
        console.log(value);
        return value;
    }

    static staticLog<OnStatic>(value: OnStatic) { // OnStatic 자체 타입 매개변수 ok
        let fromImstance: OnInstance; // error: Static members cannot reference class type parameters.(2302)
        console.log(value);
        return value;
    }
}
```

## 10.4 제네릭 타입 별칭

```tsx
type Nullish<T> = T | null | undefined
```

제네릭 타입 별칭은 일반적으로 제네릭 함수의 타입을 설명하는 함수와 함께 사용된다.

```tsx
// Input과 Output은 CreateValue제네릭함수의 타입을 설명하는데 사용됨.
type CreateValue<Input,Output> = (input:Input) => Output;

let creator: CreateValue<string,number>;

creator = (text) => text.length; // ok
creator = (text) => text.toLowerCase(); // error: Type 'string' is not assignable to type 'number'.(2322)
```

### 10.4.1 제네릭 판별된 유니언

유니언 타입을 좁힐수 있는 특정 멤버와함꼐 제네릭을 사용할 수 있다.

```tsx
type Result<Data> = FailureResult | SuccessfulResult<Data>;

interface FailureResult  {
    error: Error;
    succeeded: false;
}

interface SuccessfulResult<Data>  {
    data: Data
    succeeded: true;
}

function handleResult(result: Result<string>) {
    if(result.succeeded) {
    // result 의 타입은 SuccessfulResult 
    }else {
    // result 의 타입은 FailureResult 
    }

  // Property 'data' does not exist on type 'Result<string>'.
  //Property 'data' does not exist on type 'FailureResult'.
    result.data; 
}
```

제네릭 타입과 판별된 타입을 함께 사용하면 Result와 같은 재사용 가능한 타입을 모델링하는 훌륭한 방법을 제공할 수 있다.

## 10.5 제네릭 제한자

타입스크립트는 제네릭 타입 매개변수의 동작을 수정하는 구문도 제공한다.

### 10.5.1 제네릭 기본값

타입 매개변수에도 기본타입을 설정할 수 있다.

```tsx
interface Quote<T = string> {
    value: T;
}

let v1: Quote<number> = {value: 123};
let v2: Quote = {value: "a"};
let v3: Quote = {value: 123}; // error: Type 'number' is not assignable to type 'string'.(2322)
```

이런식으로도 사용할 수 있다.

```tsx
interface KeyValuePair<Key,Value = Key> {
    key : Key;
    value: Value;
}
```

기본값이 없는 제네릭 타입은 기본값이 있는 제네릭 타입 뒤에오면 에러발생.

```tsx
interface KeyValuePair<Key = string, Value> { // error: interface KeyValuePair<Key = string, Value> {
    key : Key;
    value: Value;
}
```

## 10.6 제한된 제네릭 타입

타입 스크립트는 타입 매개변수가 타입을 확장해야 한다고 선언할 수 있으며 별칭 타입에만 허용되는 작업이다.

타입 매개변수를 제한하는 구문은 매개변수 이름 뒤에 extends 키워드를 배치하고 그 뒤에 매개변수 타입을 제한할 타입을 배치한다.

여기서 T 타입 매개변수는 WithLength타입을 항상 포함시켜야한다.

따라서 length:number속성이 있는 문자열과 배열은 에러가 발생하지 않지만, length:number속성이 없는 Date와같은 타입형태는 에러가 발생한다.

```tsx
interface WithLength {
    length: number;
}

function logWithLength<T extends WithLength>(input: T) {
    console.log(`Length: ${input.length}`);
    return input;
}

logWithLength("a");
logWithLength([1,2,3,4]);
logWithLength(new Date()); // error 
// Argument of type 'Date' is not assignable to parameter of type 'WithLength'.
// Property 'length' is missing in type 'Date' but required in type 'WithLength'.
```

### 10.6.1 keyof와 제한된 타입 매개변수

아래의 예시와 같이 extends와 keyof를 함께 사용하면 타입 매개변수(Key)를 이전 타입 매개변수(T)의 키로 제한할 수 있다. 

Key타입 매개변수는 keyof T로 제한되기 때문에 타입스크립트는 아래의 get함수가 T[Key]를 반환 할 수 있음을 알고 있다.

```tsx
function get<T,Key extends keyof T>(container: T,key: Key) {
    return container[key]; // ok
}
```

위에서 extends로 타입을 제한했음에 주목하자.

아래에서 key매개변수의 타입을 단순히 keyof T로 작성하게되면 found의 타입은 string 이아닌 string | string[]인 유니언 타입이 된다.

```tsx
function get<T>(container: T,key: keyof T) {
    return container[key];
}

const roles = {
    favorite: "Fargo",
    others: ["a","b","c"]
}

const found = get(roles,"favorite");
```

## 10.7 Promise

자바스크립트 Promise는 네트워크 요청과 같이 요청 이후 결과를 받기까지 대기가 필요한 것을 나타낸다.

각 Promise는 대기 중인 작업이 ‘resolve(성공적으로 완료됨)’또는 ‘reject(오류 발생)’하는 경우 콜백을 등록하기 위한 메서드를 제공한다. 타입스크립트 타입 시스템에선 최종적으로 resolve된 값을 나타내는 타입 매개변수를 가진 Promise 클래스로 표현된다. 

### 10.7.1 Promise 생성

타입스크립트에서 Promise 생성자는 단일 매개변수를 받게된다. 이는 resolve되는 값의 타입을 의미한다. 명시적으로 작성하지 않게되면 unknown으로 가정한다.(reject의 타입은 unknown이다)

```tsx
// textEventually타입: Promise<string>
const textEventually = new Promise<string>((resolve) => { 
	setTimeout(() => resolve("a"),1000);
})

// textEventually타입: Promise<unknown>
const textEventually = new Promise((resolve) => {
	setTimeout(() => resolve("a"),1000);
})
```

### 10.7.2 async 함수

자바스크립트에서 async 키워드를 사용해 선언한 모든 함수는 Promise를 반환한다. 반환값이 Thenable(.then()메서드가 있는 객체)이 아닌경우, Promise.resolve가 호출된 것처럼 Promise로 래핑된다.

```tsx
// 타입 (text: string) => Promise<number>
async function lengthAfterSecond(text: string) {
    await new Promise((resolve) => setTimeout(resolve,1000))
    return text.length;
}

// 타입 (text: string) => Promise<number>
async function lengthImmediately(text: string) {
    return text.length;
}
```

## 10.8 제네릭 올바르게 사용하기

제네릭은 코드에서 타입을 설명하는데 더 많은 유연성을 제공하지만 코드가 빠르게 복잡해줄 있다. 타입스크립트의 모범사례는 필요할 때만 제네릭을 사용하고, 제네릭을 사용할 때는 무엇을 위해 사용하는지 명확히 해야한다.

### 10.8.1 제네릭 황금률

함수에 타입 매개변수가 필요한지 여부를 판단할 수 있는 간단하고 빠른 방법은 타입 매개변수가 최소 두 번 이상 사용되는지 확인하는것이다.

제네릭은 타입 관계를 설명함으로 제네릭 타입 매개변수가 한 곳에서만 나타나면 여러 타입 간의 관계를 정의할 수 없다.

아래 Input타입 매개변수는 한번만 사용된다.

```tsx
function logInput<Input extends string>(input: Input) {
    console.log("Hi!",input);
}
```

한번만 사용됨으로 별로 의미가 없기때문에 타입매개변수를 사용하지 않고 타입을 바로 지정해줘서 사용하는것과 차이가 없다.

```tsx
function logInput(input: string) {
    console.log("Hi!",input);
}
```

### 10.8.2 제네릭 명명 규칙

타입스크립트를 포함한 많은 언어가 지키는 타입 매개변수에 대한 표준 명명 규칙은 기본적으로 첫번째 타입 인수로 T를 사용하고, 후속 타입매개변수가 존재하면 U, V등을 사용한다.

타입 인수가 어떻게 사용되어야 하는지 맥락과 관련된 정보가 알려진 경우 명명 규칙은 경우에 따라 해당 용어의 첫 글자를 사용하는 것으로 확장한다. 예를들어 상태 관리 라이브러리에서는 S를, 데이터 구조의 키와 값은 K와V로 나타내기도 한다.

이는 변수의 이름이나 함수의 이름을 하나의 문자로 표한하는것과 동일할 수 있기때문에 읽기에 혼란 스럽다면 타입이 사용되는 용도를 가르키는 설명적인 제네릭 타입 이름을 사용하는 것이 좋다.

```tsx
// L과 V가 무엇일까?
function labelBox<L,V>(l:L,v:V) {}

// 좀더 명확
function labelBox<Label,Value>(l:Label,v:Value) {}
```