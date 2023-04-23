제네릭 황금률 

타입 매개변수는 여러 값의 타입을 연관시키기 위한 것이다. 

 제네릭 타입 매개변수가 한 곳에서만 나타나면 여러 타입 간 관계를 정의할 수 없다.

따라서 각 함수 타입 매개변수는 매개 변수에 사용되어야 하고, 그 다음 적어도 하나의 다른 매개변수 또는 함수의 반환 타입에서도 사용되어야 한다. 

제네릭 함수를 작성하는 것은 재미있지만, 유형 매개변수가 너무 많거나 필요하지 않은 곳에 제약 조건을 사용하면 추론이 제대로 이뤄지지 않아 호출자가 불편을 겪을 수 있다. 

이어서 제네릭을 사용하는 방법에 대한 구체적인 조언을 제공하는데. 그 중에는 이펙티브 타입스크립트의 저자가 제네릭의 황금률이라고 생각하기 시작한 것이 포함되어 있다.

함수에 타입 매개변수가 필요한지 여부를 판단할 수 있는 간단하고 빠른 방법은 타입 매개변수가 최소 두 번이상 사용되었는지 확인하는 것이다. 

이 규칙이 구체적이라서 마음에 든다고 한다. 제네릭을 사용하는 것이 좋은지 나쁜지 구체적으로 알 수 있다.

하지만 이 규칙을 적용하는 방법이 항상 명확한 것은 아니며, 제네릭을 잘못 사용하는 경우 코드를 재작업하는 방법에 대한 지침이 많지 않다.

1.

```tsx
function identity<T>(arg: T): T {
  return arg;
}
```

제네릭이 2번 나오기 때문에 사용해도 괜찮다.

2.

```tsx
function third<A, B, C>(a: A, b: B, c: C): C {
  return c;
}
```

A와 B는 한번 나오기 때문에 수정해야 한다. 

```tsx
function third<C>(a: unknown, b: unknown, c: C): C {
  return c;
}
```

3.

```tsx
function parseYAML<T>(input: string): T {
  // ...
}
```

제네릭이 한번만 나타나므로 잘못되었다. 

이러한 “return-only-generics”는 any와 같지만 명시적이지도 않아서 더 위험하다.

```tsx
interface Weight {
  pounds: number;
  ounces: number;
}

const w: Weight = parseYAML(''); // Weight타입으로 추론
```

더 나은 방법

```tsx
function parseYAML(input: string): any {
  // ...
}
```

```tsx
function parseYAML(input: string): unknown {
  // ...
}

const w = parseYAML('') as Weight;
```

```tsx
function printProperty<T, K extends keyof T>(obj : T, key : K){
	console.log(obj[key]);
}
```

K 가 한번만 사용되기 때문에, bad case라고 볼 수 있다.

아래와 같이 바꿀 수 있다. 

```tsx
function printProperty<T>(obj: T, key: keyof T) {
  console.log(obj[key]);
}
```

아래의 함수와 비슷해보인다. 

```tsx
function getProperty<T, K extends keyof T>(obj: T, key: K) {
  return obj[key];
}
```

하지만, 이 함수는 generic을 잘 사용하고 있다. (실제 함수 타입)

```tsx
function getProperty<T, K extends keyof T>(
  obj: T, key: K
): T[K]
```

반환 유형이 T[K]로 추론되기 때문에 K가 두번 나타난다. 따라서 제네릭을 잘 사용하는 예시이다. K는 T와 연관되어 있고 반환 유형은 K와 T 모두와 연관되어있다. 

```tsx
class ClassyArray<T> {
  arr: T[];
  constructor(arr: T[]) { this.arr = arr; }

  get(): T[] { return this.arr; }
  add(item: T) { this.arr.push(item); }
  remove(item: T) {
    this.arr = this.arr.filter(el => el !== item)
  }
}
```

이때 T가 여러번 나타나기 때문에 괜찮다.ClassyArray를 인스턴스화할 때 유형 변수를 바인딩하면 클래스에 있는 모든 프로퍼티와 메서드의 유형이 연관된다.

아래의 클래스는 나쁜 활용에 속한다. 

```tsx
class Joiner<T extends string | number> {
  join(els: T[]) {
    return els.map(el => '' + el).join(',');
  }
}
```

먼저 클래스가 아닌 메서드로 제네릭을 이동시킬 수 있다.

```tsx
class Joiner {
  join<T extends string | number>(els: T[]) {
    return els.map(el => '' + el).join(',');
  }
}
```

아래와 같이 리팩터링 할 수 있다. 

```tsx
class Joiner {
  join(els: (string|number)[]) {
    return els.map(el => '' + el).join(',');
  }
}

function join(els: (string|number)[]) {
  return els.map(el => '' + el).join(',');
}
```

ArrayLike객체를 받는 함수는 어떨까

```tsx
interface Lengthy {
  length: number;
}
function getLength<T extends Lengthy>(x: T) {
  return x.length;
}
```

제네릭이 한번만 나오기 때문에, 수정해야 한다. 

```tsx
function getLength(x: Lengthy) {
  return x.length;
}
//혹은
function getLength(x: {length: number}) {
  return x.length;
}
//혹은
function getLength(x: ArrayLike) { //빌트인 ArrayLike type
  return x.length;
}
```

참고) ArrayLike 인터페이스

```tsx
interface ArrayLike<T> {
    readonly length: number;
    readonly [n: number]: T;
}
```

[Effective TypeScript › The Golden Rule of Generics](https://effectivetypescript.com/2020/08/12/generics-golden-rule/)
