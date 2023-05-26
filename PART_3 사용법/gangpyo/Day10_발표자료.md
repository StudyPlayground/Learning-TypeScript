# 바벨 vs SWC

## 바벨은 무엇인가

최신버전의 자바스크립트 문법을 이전 버전의 문법으로 변환 시켜주는도구.(트랜스파일러)

자바스크립트로 작성되었다.

**변환하는 과정**

parser → traverser → generator

- parser: 자바스크립트 소스코드를 AST로 변환
- traverser: AST를  바벨 설정에서 정의한 방식으로 코드를 수정
- geerator: 수정된 AST를 코드로 호환되는 코드로 번역

## SWC(Speedy Web Compiler)는 무엇인가

바벨과 마찬기지인 트랜스파일러.

Rust로 작성되었으며 Babel보다 훨씬 더 빠르다. 이유는? 병렬처리가 가능하기때문(+가비지 컬렉션의 효율적인 처리)

## 속도 비교

동기적으로 코드 변환을 실행시키는 인위적인 환경에서 비교해봤을때

![스크린샷 2023-05-26 오후 2 39 31](https://github.com/gangpyono/TIL/assets/58588011/6f76d998-130f-41e6-88fc-5b6112c3d7ee)

→ SWC로 ES3로의 변환을 진행했음에도 불구하고 SWC의 속도가 바벨에 비해 훨씬 빠르다.(약20배)

await Promise.all()샘플을 8 CPU코어 컴퓨터기준 4개의 병렬연산이 수행되는환경에서 비교해봤을때(약60배)

4개의 프로미스 실행

![스크린샷 2023-05-26 오후 2 39 39](https://github.com/gangpyono/TIL/assets/58588011/1eefccd5-d7f6-4368-8581-618b1c3c6816)

100개의 프로미스 실행

![스크린샷 2023-05-26 오후 2 40 00](https://github.com/gangpyono/TIL/assets/58588011/474920b4-ec67-4899-864a-c6e44a6ffd97)

→ 바벨은 비동기 작업을 수행할 때 성능이 떨어지는걸 확인할 수 있다.

SWC는 싱글 스레드 CPU 코어 베이스에서 바벨보다 20배 더 빨랐고, 멀티 코어 비동기 작업 프로세스에서는 약 60배 더 빨랐다.

## 결론

SWC는 바벨에 비해 속도면에서 훨씬 빠르다.

현재 바벨을 사용하고있는데 더 빠른 빌드 시간을 얻기 위해 SWC전환을 고려중이라면 다음의 것들을 고려해보고 전환하자.

1. 모든 기능이 SWC에서 지원이 되는지.
    1. 바벨에서 사용중인 플러그인을 SWC에서도 지원해주는지?
2. 현재 사용중인 빌드 시스템이 SWC를 지원하는지.(webpack에선 지원해주더라도, Parcel과 같은 툴에선 지원해주지 않을 수 있다.)

## 참고

[[Kasra Khosravi] Why you should use SWC (and not Babel) 한글 번역](https://im-developer.tistory.com/230)

[초보 웹 개발자를 위한 자바스크립트 빌드 툴과 SWC | 카카오엔터테인먼트 FE 기술블로그](https://fe-developers.kakaoent.com/2022/220217-learn-babel-terser-swc/)