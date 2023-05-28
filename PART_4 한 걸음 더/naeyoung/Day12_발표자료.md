조건부 타입의 조건식이 참으로 평가될 때에는 `infer` 키워드를 사용할 수 있다.

`Element<number> extends Element<infer U>` 와 같은 타입을 작성하면 `U` 타입은 number 타입으로 추론된다.

이후 참인 경우에 대응되는 식에서 추론한 U타입을 사용할 수 있다. 

`T extends infer U ? X : Y`

조건부 타입

```tsx
type Result = boolean extends true ?  1 : 0;

const func = (check : true) => {};
const a = false;
func(a) // error 
```

infer 이해하기

```tsx
const func = (check: boolean) => {
	return 'hello'
}

type FuncResult = ReturnType<typeof func>;
```

- ReturnType 정의

```tsx
type ReturnType<T extends (...args: any) => any>
 T extends (...args: any) => infer R ? R : any;
```

```tsx
type Result2 = typeof func extends (...arg : any) => infer R ? R : never;
```

조건식이 참으로 평가될 때, infer 키워드를 사용하면 거기에서 타입을 추출할 수 있다. 

```tsx
type PromiseType<T> = T extends Promise<infer U> ? U : never;

typeA = PromiseType<Promise<number>> // number
typeB = PromiseType<number> // never
```

```tsx
type TailOf<T> = T extends [unknown, ...infer U] ? U : [];
type A = TailOf<[string, boolean, number]>
 // [boolean, number]
```

### 템플릿 리터럴과 함께

```tsx
type InOrOut<T> = T extends `fade${infer R}` ? R : never;
type I = InOrOut<"fadeIn"> // In
type O = InOrOut<"fadeOut"> // Out
```

```tsx
type T = TrimRight<"Hello.    ">

TrimRight<T extends string> = T extends `${infer R} ` ? TrimRight<R> : T;
```

[Template Literal Types로 타입 안전하게 코딩하기](https://toss.tech/article/template-literal-types)
