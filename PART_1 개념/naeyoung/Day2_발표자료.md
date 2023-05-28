타입스크립트에서 타입이란 가능한 값의 집합이다. 해당 변수는 오직 가능한 집합 내의 값만을 가질 수 있다. 예를 들어 `let x : number` 이라면, x에는 가능한 숫자의 집합만 할당할 수 있다. 

### 타입스크립트에서 never 타입이란 무엇일까?

타입스크립트에서 never 타입의 값은 공집합이다. 집합에 어떠한 값도 없기 때문에, any 타입도 가질 수 없는 것이다. (점유할 수 없는 bottom 타입이라고도 불린다) bottom 타입은 타입스크립트 핸드북이 never 타입을 정의하는 방식이다.  

### never 타입은 왜 필요할까?

숫자 체계에 아무것도 없는 양을 나타내는 0처럼, 문자 체계에도 **불가능을 나타내는 타입**이 필요하다. (void와 다르다. void는 함수에서 리턴 값이 없는 것을 말한다. )

- 호환되지 않는 타입의 intersection이 불가능할 때 표시됨

```tsx
type t = number & string // never
```

### never 타입의 작동 방식

- union(|)

```tsx
type a = never | string // string
```

- intersection(&)

```tsx
type b = never & string // never
```

 

### 활용 방법

- swith, if-else 문의 상황 보장

never를 기본 케이스(default case)로 이용하면 남아있는 것은 `never`
 타입이어야 하기 때문에 모든 상황에 대처하는 것을 보장할 수 있다.

```tsx
function unknownColor(x: never): never {
    throw new Error("unknown color");
}

type Color = 'red' | 'green' | 'blue'

function getColorName(c: Color): string {
    switch(c) {
        case 'red':
            return 'is red';
        case 'green':
            return 'is green';
        default:
            return unknownColor(c); // 'string' 타입은 'never' 타입에 할당할 수 없음
    }
```

- 타이핑을 부분적으로 허용하지 않음

```tsx
type VariantA = {
  a: string
  b?: never
}

type VariantB = {
  b: number
  a?: never
}

declare function fn(arg: VariantA | VariantB): void

const input = { a: 'foo', b: 123 }
fn(input) // 에러
```

- 객체 타입에서 특정 타입으로 걸러내기

```tsx
type Filter<Obj extends Object, ValueType> = {
  [Key in keyof Obj as ValueType extends Obj[Key] ? Key : never]: Obj[Key]
}

interface Person {
  name: string
  id: number
  discription : string
}

type Filtered = Filter<Person, string>
```

- 제어 흐름에서 타입을 좁히고 싶을 때

함수에서 리턴값을 `never` 로 타이핑할 때에는 함수가 실행을 마칠 때 호출자에게 제어 권한을 반환하지 않는다는 것을 명확히 알리는 것이다.

```tsx
function throwError() :never {
	throw new Error()
}
```

### never를 확인하는 법

```tsx
type IsNever<T> = T extends never ? true : false

type Res = IsNever<never> // never 
```

**`never`**는 빈 uinion이다. 타입스크립트는 조건 타입내부에 있는 유니온 타입을 자동으로 결정한다.

여기에서는 빈 uinon이 들어왔으므로, 여기에 조건 타입은 다시 **`never`**가 된다.

따라서 우리가 생각하는 **`IsNever`**의 목적을 달성하기 위해서는 아래와 같은 튜플을 이용하는 방식을 취해야 한다.

```tsx
type IsNever<T> = [T] extends [never] ? true : false
type Res1 = IsNever<never>// 'true'
type Res2 = IsNever<number>// 'false' 
```

https://yceffort.kr/2022/03/understanding-typescript-never

[[TS] 📘 타입스크립트 - 조건부 타입 완벽 이해하기](https://inpa.tistory.com/entry/TS-📘-타입스크립트-조건부-타입-완벽-이해하기)

[Never Type](https://basarat.gitbook.io/typescript/type-system/never)
