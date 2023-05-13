---
marp: true
paginate: true
---

# 컴파일러 (Babel vs Typescript)

---

## 컴파일러

- 바벨: JS -> 여러 브라우저 및 환경에서 지원되는 JS 코드로 변환
- 타입스크립트: TS -> JS

---

## 바벨7 이전

1. TS -> [TS 컴파일러] -> JS
2. JS -> [바벨] -> 여러 환경을 지원되는 JS 코드

---

## 바벨7 이후

- TS -> [바벨] -> JS
- 타입스크립트 코드를 제거하고 이후 작업 진행
- 장점
  1. 바벨이 캐싱과 같은 여러 기능을 제공
  2. 타입 검사를 거치지 않아 컴파일이 매우 빠름

---

## 참고

- Chapter13: 구성 옵션
- [바벨과 타입스크립트의 아름다운 결혼](https://ui.toast.com/weekly-pick/ko_20181220)
- [babel-loader와 ts-loader의 빌드 결과가 다른 현상](https://jeonghwan-kim.github.io/dev/2021/03/08/babel-typescript.html)
- [TypeScript and Babel 7](https://devblogs.microsoft.com/typescript/typescript-and-babel-7/)