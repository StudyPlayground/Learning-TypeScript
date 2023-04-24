### 편의성 제네릭

한번만 쓰이는 제네릭은 type assertion과 동일하다. 

```tsx
declare function parse<T>(name: string): T;

declare function parse(name: string): any;
const something = parse('something') as TypeOfSomething;
```

한 번만 사용되는 Generic은 타입 안정성이란 측면에서는 type assertion보다 딱히 더 나은 점이 없습니다. 그러나 Generic은 API에 *편의성*을 제공한다. 

좀 더 명백한 예로는 json response를 로드하는 함수를 들 수 있다. 그 함수는 *당신이 어떤 타입을 전달하든지 간에* 해당 타입의 Promise를 리턴한다.

```tsx
const getJSON = <T>(config: {
    url: string,
    headers?: { [key: string]: string },
  }): Promise<T> => {
    const fetchConfig = ({
      method: 'GET',
      'Accept': 'application/json',
      'Content-Type': 'application/json',
      ...(config.headers || {})
    });
    return fetch(config.url, fetchConfig)
      .then<T>(response => response.json()); // 항상 해당 타입의 Promise return 
  }
```

```tsx
type LoadUsersResponse = {
  users: {
    name: string;
    email: string;
  }[]; // user 객체의 배열
}
function loadUsers() {
  return getJSON<LoadUsersResponse>({ url: 'https://example.com/users' });
}
```

그리고 `Promise<T>`를 리턴 값으로 정의하는 것이 `Promise<any>`를 사용하는 것보다 훨씬 낫다. (주장)

또 다른 예시로 Generic이 함수 인자로만 사용되는 경우가 있다. 

```tsx
declare function send<T>(arg: T): void;
```

[https://radlohead.gitbook.io/typescript-deep-dive/type-system/generics#convenience-generic](https://radlohead.gitbook.io/typescript-deep-dive/type-system/generics#convenience-generic)

[https://github.com/microsoft/TypeScript/issues/44603](https://github.com/microsoft/TypeScript/issues/44603)

왜 json.parse는 unknown이 아닌 any일까

[https://github.com/microsoft/TypeScript/issues/26188](https://github.com/microsoft/TypeScript/issues/26188)

재선언하기

```tsx
declare global {
  interface JSON {
    parse(text: string, reviver?: (key: any, value: any) => any): unknown;
  }

  interface ArrayConstructor {
    isArray(a: unknown): a is unknown[];
  }

  interface Body {
    json(): Promise<unknown>;
  }
}
```
