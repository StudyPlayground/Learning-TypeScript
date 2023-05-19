# CHAPTER 15 타입 운영
과하게 사용하면 읽기 어려울 수 있으니 유념하자.

## 15.1 매핑된 타입

하나의 타입에서 다른 타입으로 매핑시킨다.

in을 사용해 다른 타입으로부터 계산된 타입을 가져온다.

```tsx
type NewType = {
	[K in OriginalType]: NewProperty;
}
```

유니언 타입에 존재하는 각 문자열 리터럴 키를 가진 객체를 생성하려고 할때 사용.

### 15.1.1 타입에서 매핑된 타입

일반적으로 매핑된 타입은 keyof 연산자를 사용해 키를 가져오는 방식으로 작동한다.

```tsx
interface AnimalVariants {
    alligator: boolean;
    baboon:number;
    cat: string;
}

type AnimalCounts = {
    [K in keyof AnimalVariants]: number;
}

```

```tsx
interface BirdVariants {
    dove: string;
    eagle: boolean;
}

type NullableBirdVariants {
    [K in keyof BirdVariants]: BirdVariants[K] | null;
}
 
// {
//		dove : string | null;
//		eagle: boolean | null;	
// }
```

### 15.1.2 제한자 변경

매핑된 타입은 원래 타입의 멤버에 대해 접근 제어 제한자인 readonly와 ?도 변경 가능하다.

```tsx
interface Person {
    name: string;
    age: number;
}

type ReadonlyPerson = {
    readonly [K in keyof Person]: Person[K];
}

type OptionalPerson = {
    [K in keyof ReadonlyPerson]: ReadonlyPerson[K];
}
```

새로운 타입 제한자 앞에 -를 추가해 제한자를 제거할 수 있다.

```tsx
interface Person {
    readonly name: string;
    aga?: number;
}

type WritablePerson = {
    -readonly [K in keyof Person]: Person[K];
}

type RequiredWritablePerson = {
    [K in keyof WritablePerson]-?: WritablePerson[K];
}
```

### 15.1.3 제네릭 매핑된 타입

매핑된 타입의 완전한 힘은 제네릭과 결합해 단일 타입의 매핑을 다른 타입에서 재사용 할 수 있도록 하는 것에서 나온다.

```tsx

type MakeReadonly<T> = {
	readonly [K in keyof T]: T[K];
}

interface Species {
	genus: string;
	name: string;
}

type ReadonlySpecies = MakeReadonly<Species>;
// 다음과 같음
// {
//.  readonly genus: string;
//.  readonly name: string;
// {
```

타입스크립트는 이런 제네릭 매핑된 타입을 즉시 사용할 수있도록 여러 내장 유틸리티 타입을 제공한다.

## 15.2 조건부 타입

조건부 타입에서 논리적 검사는 항상 extends의 왼쪽 타입이 오른쪽 타입이 되는지 또는 할당 가능한지 여부에 있다.

```tsx
LeftType extends RightType ? IfTrue : IfFalse
```

### 15.2.1 제네릭 조건부 타입

새로운 타입을 생성하기위해 재사용 가능한 제네릭 타입을 사용할 수 있다.

```tsx
type CheckAgainstNumber<T> = T extends number ? true : false
```

### 15.2.2 타입 분산

조건부 타입은 유니언에 분산된다.

ConditionmalType<T | U> 는 Conditional<T> | Conditional<U> 와 같다.

```tsx
type ArrayifUnlessString<T> = T extends string ? T : T[];

type A = ArrayifUnlessString<string | number>;
// A: string | number[]
```

조건부 타입은 전체 유니언 타입이 아니라 유니언 타입 각 구성 요소에 로직을 적용한다.

### 15.2.3 유추된 타입

함수 매개변수또는 반환 타입 같은 정보에서 타입의 멤버에 접근 하는 방법.

extends 절에 infer키워드로 접근.

```tsx
type ArrayItems<T> = T extends (infer Item)[] ? Item : T;

// 타입: string
type StringItem = ArrayItems<string>;
type StringArrayItem = ArrayItems<string[]>;

// 타입: string[]
type String2DItem = ArrayItems<string[][]>;
```

### 15.2.4 매핑된 조건부 타입

매핑된 타입은 기존 타입의 모든 멤버에 변경 사항을 적용하고, 조건부 타입은 하나의 타입에 변경 사항을 적용한다.

## 15.3 never

bottom타입인 never는 가능한 값을 가질 수 없고 접근 할 수 없음을 의미한다.

올바른 위치에 never타입 애너테이션을 추가하면 타입스크립트가 이전 런타임 예제 코드뿐만 아니라 타입 시스템에서 맞지 않는 코드경로를 더 공격적으로 탐지한다.

### 15.3.1 never타입과 교차, 유니언타입

never타입이 교차 타입(&)과 유니언타입(|)과 함께 사용되면 다음과같이 동작한다.

- 교차 타입(&)에 있는 never는 교차 타입을 never로 만든다.
- 유니언 타입(|)에 있는 never는 무시된다.

```tsx
type A = never & string; // 타입: never
type B = never | string; // 타입: string
```

유니언 타입에서 never타입이 무시되는 동작은 조건부 타입과 매핑된 타입에서 값을 필터링하는데 유용하다.

### 15.3.2 never와 조건부 타입

제네릭 조건부 타입은 일반적으로 유니언 타입에서 타입을 필터링하기 위해 never를 사용한다.

### 15.3.3 never와 매핑된 타입

유니언에서 never의 동작은 매핑된 타입에서 멤버를 필터링 할 때도 유용하다.

- 유니언에서 never는 무시
- 매핑된 타입은 타입의 멤버를 매핑할 수 있다.
- 조건부 타입은 조건이 충족되는 경우 타입을 never로 변환하는데 사용할 수 있다.

## 15.4 템플릿 리터럴 타입

문자열 타입을 나타내는 방법

- 원시 string타입: 값이 세상의 모든 문자열이 될 수 있는 경우
- “”와 “abc” 같은 리터럴 타입: 값이 오직 한 가지 타입만 될 수 있는 경우

여기에 추가로 일부 문자열만 일치한지를 나타내는데 사용되는 타입 → **템플릿 리터럴 타입**

### 15.4.1 고유 문자열 조작 타입

문자열 타입 작업을 지원하기 위해 문자열을 가져와 문자열에 일부 조작을 적용하는 고유 제네릭 유틸리티 타입을 제공한다.

### 15.4.2 템플릿 리터럴 키

템플릿 리터럴 타입은 문자열 리터럴을 사용할 수 있는 모든 위치에서 사용 가능하다.

예를 들어 매핑된 타입의 인덱스 시그니처로도 사용이 가능하다.

### 15.4.3 매핑된 타입 키 다시 매핑하기

템플릿 리터럴 타입을 사용해 기존에 멤버를 기반으로 매핑된 타입의 멤버에 대한 새로운 키를 생성할 수 있다.

### 15.5 타입 운영과 복잡성

타입 운영을 사용해야 하는 경우에는 향후 코드를 읽어야 하는 모든 개발자를 위해 가능한 한 최소한으로 사용하도록 노력하자. 코드를 읽는 사람이 이해하기 쉬운 이름을 사용하고, 미래에 코드를 읽을 때 어려움을 겪을 수 있다고 생각되는 모든 부분에 설명을 남기자