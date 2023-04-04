
## type와 interface의 차이 (version1)

## 공통점

type과 interface의 기본적인 사용법은 같다.

```jsx
interface Time {
	hour : string;
    minute : string;
    second : string;
}

Type Time {
	hour : string;
    minute : string;
    second : string;
}
```

ts 공식문서에도 비슷하기 때문에 많은 경우에는 그냥 둘 중 하나를 쓰면 된다고 한다.

## 차이점

### 1. 선언 타입

interface는 객체를 선언할 때에만 사용 가능하고, type은 원시형 type에도 사용가능하다.

```jsx
// Here's two examples of
// using types and interfaces
// to describe an object

interface AnObject1 {
    value: string
}

type AnObject2 = {
    value: string
}

// Using type we can create custom names
// for existing primitives:

type SanitizedString = string
type EvenNumber = number

// This isn't feasible with interfaces
interface Xextends string {

}
```

### 2. 확장

type은 & 로 확장하지만, interface는 extends로 확장한다. 

```jsx
//interface
interface Animal {
  name: string
}

interface Bearextends Animal {
  honey: boolean
}

const bear = getBear()
bear.name
bear.honey

//type

type Animal = {
  name: string
}

type Bear = Animal & {
  honey: Boolean
}

const bear = getBear();
bear.name;
bear.honey;
```

### 3. 선언 병합

interface는 언제든 re-opend 될 수 있고, 새로운 값이 추가될 수 있다.

interface는 동일한 이름으로 여러번 선언하면 합쳐진다. (컴파일 시점에 병합됨)

type은 그럴 수 없다. 에러가 난다. 

```jsx
interface Mammal {
    genus: string
}

interface Mammal {
    breed?: string
}

const animal: Mammal = {
    genus: "1234",
    // Fails because breed has to be a string
    breed: 1
}

type Reptile = {
    genus: string
}

// You cannot add new variables in the same way
type Reptile = {
    breed?: string
}
```

### typescript 에서는..

ts에서는 type보다는 [개방-폐쇄 원칙](https://ko.wikipedia.org/wiki/%EA%B0%9C%EB%B0%A9-%ED%8F%90%EC%87%84_%EC%9B%90%EC%B9%99)에 따라 만든 interface를 사용하는 쪽을 권장하고 있다. 하지만 둘 다 별로 상관 없다고는 한다.

ts 공식 문서에서는 잘 모르겠으면 일단 interface로 작업하라고 하기 때문에 interface로 작업하는게 나을 듯 싶다.

*대부분의 경우 개인적 선호에 따라 인터페이스와 타입 중에서 선택할 수 있으며, 필요하다면 TypeScript가 다른 선택을 제안할 것입니다. 잘 모르겠다면, 우선 interface를 사용하고 이후 문제가 발생하였을 때 type을 사용하기 바랍니다.*

[타입스크립트 type과 interface의 공통점과 차이점](https://yceffort.kr/2021/03/typescript-interface-vs-type)




## type와 interface의 차이 (version2)


1. 선언

인터페이스는 객체 타입만 선언이 가능하다. type은 모든 타입에 이름을 달 수 있다. 

```tsx
type UserId = string; // 기본형
type VideoUpload = [UserId, Video] // 튜플
```

1. IDE에서의 차이 

hover시 type은 구체적인 타입 요소가 뜨는 반면, interface는 이름만 뜬다. 

```tsx
type TypeA = {
  a: number
}

const a: TypeA = {
  a: 1,
}

interface InterfaceA {
  a: number
}

const b: InterfaceA = {
  a: 1,
}
```

1. 속도 차이

타입보다 인터페이스가 더 빠르다고 알려져있다. 하지만 현재 타입 검사기의 속도가 발전해서 현재는 거의 차이가 나지 않는 것 같다. 

1. interface의 선언 병합

인터페이스는 선언 병합이 가능하다. 동일한 이름으로 선언을 하면 타입 추가를 할 수 있다.

```tsx
interface a {
a : number;
}

interface a {
b : number;
}

// interface a {
	// a : number;
	// b : number;
//}
```

⇒ 실제로 어떤 타입인지 모른다. 

타입으로 선언하면 식별자가 중복되었다고 하면서 에러가 발생한다. 

이것 때문에 확장성을 가진 interface를 쓰라고 이야기하고 있는데, 실제 프로젝트에서는 interface를 사용하다 의도하지 않은 선언 병합이 발생한 경우가 있을 수 있다. 

1. 유니온 타입 확장

```tsx
type User2 = {
	id : string
} | {
	id : number
}

type Admin2 = User2 & {
	type : 'admin'
} // ok

interface Admin3 extends User2 { // error 
	type : 'admin'
}
```

[https://techblog.woowahan.com/9804/#toc-3](https://techblog.woowahan.com/9804/#toc-3)

[https://www.youtube.com/watch?v=zM9UPcIyyhQ&t=1s](https://www.youtube.com/watch?v=zM9UPcIyyhQ&t=1s)
