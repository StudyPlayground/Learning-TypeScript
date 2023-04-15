# private keyword vs  private fields
## 1. private keyword

타입스크립트의 타입 시스템에만 존재하고 타입스크립트 코드가 자바스크립트 코드로 컴파일되면 사라진다.

```jsx
// ts 
class Person {
    private name: string;
    constructor(name : string) {
        this.name = name;
    }
}
const kevin = new Person("Kevin");
kevin.name; // error
```

따라서 다음과같은 타입 단언을 선언해주면 개발과정에서 타입체킹 오류가 발생하지않게됨으로써 런타임에서도 정상 동작하는 형태로 코드를 작성할 수 있다.

```tsx
class Person {
    private name: string;
    constructor(name : string) {
        this.name = name;
    }
}
const kevin = new Person("Kevin");
(kevin as any).name // ok
```

→ private keyword는 자바스크립트로 컴파일되고 실제로 동작하는 런타임환경에서는 일반 프로퍼티에 불과하여 상대적으로 보호가 덜 된다.

## 2. private fields

자바스크립트의 속성으로 런타임에서도 접근이 불가능하다.

```tsx
class Person {
    #name: string;
    constructor(name : string) {
        this.#name = name;
    }
}
const kevin = new Person("Kevin");
kevin.name; // error
```

→ private keyword에비해 좀더 완벽한 private를 보장해준다.

## 무엇을 사용해야할까

런타임에서도 private를 유지하려면 private fields를 사용하는게 맞지만, private fields는 22년도에 tc39위원회에서 4단계로 승인된만큼 매우 최신문법이다. 따라서 브라우저의 호환성에대한 문제가 발생할 수도 있다.

또한 지금까지 타입스크립트개발자가 private keyword를 사용해 개발을 진행하는데 큰 무리가 없어왔고 브라우저의 호환성측면에서 private keyword가 좀더 대중적이지 않나 싶다. (하지만 런타임에서의 차이점이 분명이 존재하긴한다)

## reference

[What are the differences between the private keyword and private fields in TypeScript?](https://stackoverflow.com/questions/59641564/what-are-the-differences-between-the-private-keyword-and-private-fields-in-types)

자바스크립트 표준을 정의하는 tc39위원회

[TC39 – Specifying JavaScript.](https://tc39.es/)

tc39 프로세스 

[The TC39 Process](https://tc39.es/process-document/)

[게으른 개발자 | 게으른 개발자](https://trustyoo86.github.io/javascript/2019/12/11/tc39-process.html)

레포에 승인된 커밋

[https://github.com/tc39/proposal-private-fields](https://github.com/tc39/proposal-private-fields)