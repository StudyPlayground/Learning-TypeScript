# 트리플 슬래시 지시어?
트리플-슬래시 지시어는 단일 XML 태그를 포함한 한 줄 주석이다. 주석의 내용은 **컴파일러 지시어**로 사용된다. 트리플-슬래시 지시어는 파일의 상단에 위치시켜야하고, 그렇지 않을경우 일반 주석으로 여겨진다.

### reference 태그가 존재하나?

**chat-gpt**

Q. Is the reference tag used in <reference no-default-lib="true"/> XML grammar?

A. **`<reference no-default-lib="true"/>`** 태그는 XML 문법이 아니라 TypeScript에서 사용되는 특별한 태그입니다. TypeScript에서는 이 태그를 사용하여 컴파일러가 특정 파일을 참조하도록 지시하거나, 필요한 라이브러리를 로드하도록 지정할 수 있습니다. 따라서 **`<reference no-default-lib="true"/>`** 태그는 XML 문법이 아니며, TypeScript에서만 사용되는 문법입니다.

![스크린샷 2023-05-07 오전 1 13 58](https://user-images.githubusercontent.com/58588011/236987013-be691b22-043d-47de-accb-1c4838676721.png)

## 1. `/// <reference no-default-lib=”true”/>`

컴파일러 기본 라이브러리(예. `lib.d.ts`) 를 컴파일에 포함시키지 *않도록* 지시하기 때문에(명령 줄에 `--noLib`을 넘겨주는 것과 비슷한 영향을 준다.), 기본 라이브러리를 수동으로 포함시켜야함을 의미한다.

### —noLib 명령어?

커맨드 라인에서 `--noLib` 플래그를 명시하거나 `tsconfig.json`에 `"noLib": true`를 추가하면, 컴파일 context에서 `lib.d.ts`가 추가되는 것을 막을 수 있다.

**적용 전**

![스크린샷 2023-05-07 오전 1 11 19](https://user-images.githubusercontent.com/58588011/236987046-f99d0209-0841-428c-8fae-bfd3bdd7aa35.png)



**적용 후**

![스크린샷 2023-05-07 오전 1 08 48](https://user-images.githubusercontent.com/58588011/236986812-4700dd5c-56b6-407c-bce4-64e3fb94c867.png)

![스크린샷 2023-05-07 오전 1 10 46](https://user-images.githubusercontent.com/58588011/236986892-d6b3874a-4471-4af6-a9d9-7f82192928cd.png)


## 2. `/// <reference lib= “…”/>`

이 지시어는 컴파일 프로세스에 포함시킬 타입 라이브러리를 지시하는데 사용된다.

예를 들어 TypeScript 파일에 `/// <reference lib="es5" />` 지시문을 포함하면 컴파일러는 `es5.d.ts` 타입 정의 파일을 포함합니다.

파일중 `/// <reference lib="es2017.string" />`를 추가한 것은 `--lib es2017.string`으로 컴파일하는 것을 의미한다.

```tsx
/// <reference lib="es2017.string" />
"foo".padStart(4); // ok
```


## 참고자료

[Documentation - Triple-Slash Directives](https://www.typescriptlang.org/ko/docs/handbook/triple-slash-directives.html)

[lib.d.ts](https://radlohead.gitbook.io/typescript-deep-dive/type-system/lib.d.ts#libdts-뜯어보기)