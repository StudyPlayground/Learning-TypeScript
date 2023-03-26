# noUncheckedIndexedAccess option
1. noUncheckedIndexedAccess: false

```tsx
// 1.
const array = [1,2,3,4];
const v1 = array[0] // v1:number

// 2. 
export const myObj: Record<string, string[]> = {}
myObj.foo.push("bar") // 런타임 에러(타입체크는 통과)
```

1. noUncheckedIndexedAccess: true

```tsx

// 1.
const array = [1,2,3,4];
const v1 = array[0] // v1:number | undefined

// 2. 
export const myObj: Record<string, string[]> = {}

if (!myObj.foo) {
  myObj.foo = []
}

myObj.foo.push("bar") // ok
```

→ 배열에 대한 타입을 좀 더 엄격하게 관리할지에 대한 옵션.

## Reference

[Make accessing objects safer by enabling 'noUncheckedIndexedAccess' in tsconfig](https://www.totaltypescript.com/tips/make-accessing-objects-safer-by-enabling-nouncheckedindexedaccess-in-tsconfig)