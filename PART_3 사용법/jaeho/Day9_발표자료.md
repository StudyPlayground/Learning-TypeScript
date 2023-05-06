---
marp: true
paginate: true
---

# 외부 라이브러리 타입

---

## 외부 라이브러리 타입 가져오기

- 외부 라이브러리에서 타입 선언한 경우
  - NPM 홈페이지에서 라이브러리 이름 옆에 `TS 표시` 여부에 따라 타입 선언 여부를 알 수 있음
  - 예시: [axios](https://www.npmjs.com/package/axios)
- 외부 라이브러리에서 타입 선언하지 않은 경우
  - `Definitely Typed` 라이브러리로 부터 설치
    - 위의 `TS 표시` 말고 `DT 표시`가 있음
      - 예시: [lodash](https://www.npmjs.com/package/lodash)
    - `@types/라이브러리명` 으로 NPM 홈페이지에서 검색
      - 예시: [@types/lodash](https://www.npmjs.com/package/@types/lodash)
  - 직접 `.d.ts` 파일 작성

---

## 직접 `.d.ts` 파일 작성하기

1. 타입 선언이 없는 외부 라이브러리 설치 [(예: hex-to-binary)](https://www.npmjs.com/package/hex-to-binary)
2. tsconfig.json 파일 (compilerOptions 옵션 중)
    ```
    "typeRoots": ["./node_modules/@types", "./types"]
    // 기본값은 ["./node_modules/@types"]로 되어 있는데
    // 작성한 선언파일을 작성한 폴더 추가 ("./types")
    ```

---

## 직접 `.d.ts` 파일 작성하기

3. `프로젝트명/types` 폴더 내부에 다음과 같은 방법으로 작성
   - 외부라이브러리명/index.d.ts
   - 외부라이브러리명.d.ts
4. 선언 파일 작성
    ```
    declare module "hex-to-binary" {
        export default function hexToBinary(s: string): string;
    }
    ```

---

## 참고

- Chapter11: 선언 파일
- [7 최종 프로젝트 - 외부 라이브러리 모듈화](https://hyungju-lee.github.io/hyungju-lee2021_2.github.io/categories/study/typescript-advanced/typescript-advanced7.html)