# PromiseLike, ArrayLike 타입?
타입스크립트에는 PromiseLike, ArrayLike타입이 존재한다. 무엇을 의미하는걸까?

## 1. Array vs ArrayLike

### ArrayLike

lib.ed5.d.ts

```tsx
interface ArrayLike<T> {
    readonly length: number;
    readonly [n: number]: T;
}
```

키가 숫자고, length라는 속성을 가진객체란 **유사배열 객체**를 의미한다. 대표적으론 arguments 객체가 있다. 

### Array

lib.ed5.d.ts

```tsx
interface Array<T> {
    length: number;
    toString(): string;
    toLocaleString(): string;
    pop(): T | undefined;
    push(...items: T[]): number;
    concat(...items: ConcatArray<T>[]): T[];
    concat(...items: (T | ConcatArray<T>)[]): T[];
    join(separator?: string): string;
    reverse(): T[];
    shift(): T | undefined;
    slice(start?: number, end?: number): T[];
    sort(compareFn?: (a: T, b: T) => number): this;
    splice(start: number, deleteCount?: number): T[];
    splice(start: number, deleteCount: number, ...items: T[]): T[];
    unshift(...items: T[]): number;
    indexOf(searchElement: T, fromIndex?: number): number;
    lastIndexOf(searchElement: T, fromIndex?: number): number;
    every<S extends T>(predicate: (value: T, index: number, array: T[]) => value is S, thisArg?: any): this is S[];
    every(predicate: (value: T, index: number, array: T[]) => unknown, thisArg?: any): boolean;
    some(predicate: (value: T, index: number, array: T[]) => unknown, thisArg?: any): boolean;
    forEach(callbackfn: (value: T, index: number, array: T[]) => void, thisArg?: any): void;
    map<U>(callbackfn: (value: T, index: number, array: T[]) => U, thisArg?: any): U[];
    filter<S extends T>(predicate: (value: T, index: number, array: T[]) => value is S, thisArg?: any): S[];
    filter(predicate: (value: T, index: number, array: T[]) => unknown, thisArg?: any): T[];
    reduce(callbackfn: (previousValue: T, currentValue: T, currentIndex: number, array: T[]) => T): T;
    reduce(callbackfn: (previousValue: T, currentValue: T, currentIndex: number, array: T[]) => T, initialValue: T): T;
    reduce<U>(callbackfn: (previousValue: U, currentValue: T, currentIndex: number, array: T[]) => U, initialValue: U): U;
    reduceRight(callbackfn: (previousValue: T, currentValue: T, currentIndex: number, array: T[]) => T): T;
    reduceRight(callbackfn: (previousValue: T, currentValue: T, currentIndex: number, array: T[]) => T, initialValue: T): T;
    reduceRight<U>(callbackfn: (previousValue: U, currentValue: T, currentIndex: number, array: T[]) => U, initialValue: U): U;
    [n: number]: T;
}
```

코드로 확인해보자.

```tsx
function getSize(arr: Array<any>): number {
    return arr.length;
}

console.log(getSize([1, 2, 3])); // ok

function f1() {
    console.log(getSize(arguments)); // error
}
```

위에서 getSize함수의 매개변수arr의 타입을 Array에서 ArrayLike타입으로 변경해주면 정상동작한다.

```tsx
function getSize(arr: ArrayLike<any>): number {
    return arr.length;
}

console.log(getSize([1, 2, 3])); // ok

function f1() {
    console.log(getSize(arguments)); // ok
}
```

**→ ArrayLike는 유사배열 객체를위한 타입을 제공해준다.**

## 2. Promise vs PromiseLike

### PromiseLike

lib.ed5.d.ts

```tsx
interface PromiseLike<T> {
    then<TResult1 = T, TResult2 = never>(onfulfilled?: ((value: T) => TResult1 | PromiseLike<TResult1>) | undefined | null, onrejected?: ((reason: any) => TResult2 | PromiseLike<TResult2>) | undefined | null): PromiseLike<TResult1 | TResult2>;
}
```

### Promise

lib.ed5.d.ts

```tsx
interface Promise<T> {
    then<TResult1 = T, TResult2 = never>(onfulfilled?: ((value: T) => TResult1 | PromiseLike<TResult1>) | undefined | null, onrejected?: ((reason: any) => TResult2 | PromiseLike<TResult2>) | undefined | null): Promise<TResult1 | TResult2>;
    catch<TResult = never>(onrejected?: ((reason: any) => TResult | PromiseLike<TResult>) | undefined | null): Promise<T | TResult>;
}
```

lib.es2018.promise.d.ts

```tsx
interface Promise<T> {
    finally(onfinally?: (() => void) | undefined | null): Promise<T>
}
```

위처럼 타입 파일을 확인해보면 PromiseLike타입파일에는 catch와 finally메서드가 정의되어 있지 않다.코드를 통해 확인해보자. 타입스크립트의 구조적 타입 시스템에의해 Promise는 PromiseLike타입을 갖는 변수(v)에 할당할 수 있다.

```tsx
const v: PromiseLike<string> = new Promise<string>((resolve, reject) => {
      resolve("v2");
    });

v.then(() => {}) // ok
v.catch(() => {}) // error: Property 'catch' does not exist on type 'PromiseLike<string>'.(2339)
v.finally(() => {}) // error: Property 'finally' does not exist on type 'PromiseLike<string>'.(2339)
```

그래서 PromiseLike는 왜존재할까?

**→ 현재 ECMA2016에 채택되어 사용중인 promise문법은 Promise/A+의 사양을 따른다하며(공식적이진 않다), Promise가 정식 스펙으로 되기 전 Promise를 구현하기위한 다양한 라이브러리의 호환성때문이지 않을까 추측해본다.**

## 3.참고자료

[Array vs ArrayLike, Promise vs PromiseLike](https://yceffort.kr/2021/11/array-arraylike-promise-promiselike)

- 유사배열객체: 키가 숫자고, length라는 속성을 가지지만, 배열 매서드를 가지고 있지 않는 객체

[arguments 객체 - JavaScript | MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Functions/arguments)

- JavaScript Promise 동작을 표준화하기 위한 사양

[Promises/A+](https://promisesaplus.com/)

- 표준스펙에서 catch를 처리하는 방식

[ECMAScript® 2024 Language Specification](https://tc39.es/ecma262/#sec-promise.prototype.catch)

- 현재 사용중인 Promise문법이 Promise/A+의 사양을 반영한다는 QnA

[Is the JavaScript ES6 promise exactly the same as Promise/A+?](https://stackoverflow.com/questions/59503772/is-the-javascript-es6-promise-exactly-the-same-as-promise-a)