noUncheckedIndexedAccess

Typescript는 인덱스 서명을 통해 알 수 없는 키가 있지만 객체에 대한 값이 알려진 값에 대해서 설명할 수 있습니다. 

```tsx
interface EnvironmentVars {
  NAME : string;
  OS : string;
	// index signature에 알수 없는 속성이 포함됩니다. 
	[propName : string] : string;
}

declare const env : EnvironmentVars;

// 선언에 의해 존재를 앎
const sysName = env.NAME; // string
const os = env.OS; // string

// 선언되지 않았지만 index signature로 인해 string으로 간주됨
const nodeEnv = env.NODE_ENV; // string

```

`noUncheckedIndexedAccess` 를 를 키면, `undefined` 가 선언되지 않은 필드에 추가됩니다. 

```tsx

// 선언에 의해 존재를 앎
const sysName = env.NAME; // string
const os = env.OS; // string

// undefined로 됨
const nodeEnv = env.NODE_ENV; // string | undefined
```

### 배열일 시

```tsx
const strings: Array<string> = ['hello', 'every', 'body'];

const test = strings[2] // should be number | undefined instead of number
console.log(test);
console.log(typeof test);

if(test){
    console.log(test.length);
}
```

[https://github.com/microsoft/TypeScript/issues/49169](https://github.com/microsoft/TypeScript/issues/49169)

[https://github.com/microsoft/TypeScript/pull/39560](https://github.com/microsoft/TypeScript/pull/39560)

깃허브 이슈 

- 해당 모드를 default로!

안되는 이유 → c 스타일 루프(for 같은)에서 에러를 일으킴
