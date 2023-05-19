---
marp: true
paginate: true
---

# import type

---

## 예시 코드

```ts
// value1.ts (동일한 코드로 value2.ts, value3.ts, value4.ts 파일 있다고 가정)
type value1Type = string;
const value1: value1Type = "abc";
export { value1, value1Type };

// index.ts
import { value1, value1Type } from "./value1";
import type { value2, value2Type } from "./value2";
import { type value3, value3Type } from "./value3";
import { value4, type value4Type } from "./value4";

const value2_ = value2;
// ERROR: 'value2' cannot be used as a value because it was imported using 'import type'.
```

---

## import type은

- `일반 변수`를 가져오는 경우
  - import type은 타입스크립트에게 현재 파일에서는 type으로만 사용할 것이다 라는 것을 알려주는 것
  - 주의할 부분은 변수의 타입을 가져온다는 것이 아니다.
  - 사용할때 typeof와 같은 키워드를 함께 사용해야 한다.
- `타입`을 가져오는 경우
  - 이미 타입으로 타입스크립트가 인식하고 있기 때문에 값으로서 사용될 때 타입스크립트가 알아서 오류를 준다.

---

## 이러한 기능의 배경

> 여러 배경(원인) 중 하나이겠지만

- 코드를 파일 단위로 트랜스파일하는 사용자(바벨, transpileOnly 모드의 ts-loader에서와 같이)는 단일 파일 트랜스파일 중에 타입의 재내보내기를 제거해야 하지만 컴파일러가 재내보내기가 단지 타입이라는 것을 알 수 없는 반대의 문제를 겪기도 합니다.
- [관련 코드](https://github.com/TypeStrong/ts-loader/issues/751#issue-308217690)

---

## 참고

- Chapter14: 구문 확장
- [Type-only imports and exports](https://github.com/microsoft/TypeScript/pull/35200)
- [When transpileOnly is enabled, ts-loader leaves in re-exports of types, causing webpack 4 to warn about export not being found](https://github.com/TypeStrong/ts-loader/issues/751)
- [예시 코드](https://www.typescriptlang.org/play?#code/PTAEDcEMBsFcFMCMA6ALgZ1ACkJvNgfccB1V0QFXnAdlsB0OiGBAJgBoq54BmBqJgFlEBhlvUQCPHAJ02ADmtCAAGsCoEwEoAUKgCeAB3iMEiACpKVAXlDpUAJwCWAOwDmAbhkBjAPYn9qpAC4nGraF0AiSACNrXlbwAB6KtgaooADebmzUSJrKoAC+VjIgoKYAJiFo6DJGALZhEdGxbokqyaAAZga2haBeyMDsaoEFxeGRCkkxbfD0TjSVKbX1jc2t8TQdRSWRMb0qA6xOzKPVdQ1NLatzXaX98RwMy04cm+M7UwMcHTb2jgM0APqew1YZAKIASr8AeV+rgA5C8QaBrJATCZbJFfCpYOh4FlQJBMJAnKAEVCkSojJEAO7ozKHVAo0BI0xmUAg+bdUDLEHIIA)
