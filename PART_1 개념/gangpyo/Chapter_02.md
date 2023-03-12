# CHAPTER 2 타입 시스템

## 2.1 타입의 종류

- 가장 기본적인 타입은 자바스크립트의 7가지의 원시타입과 동일하다.
- ts는 계산된 초깃값을 갖는 변수의 타입을 유추할 만큼 충분히 똑똑하다.

```jsx
let bestSong = Math.random() > 0.5 ? "Chain of Fools" : "Respect";
// ts는 bestSong의 타입을 string으로 유추한다.
```

### 2.1.1 타입 시스템

- 타입 시스템은 프로그래밍 언어가 프로그램에서 가질 수 있는 타입을 이해하는 방법에대한 규칙 집합이다.
- 타입스크립트에서 타입시스템의 동작순서
    
    ```jsx
    let firstname = "Whitney";
    firstName.length(); // Error
    ```
    
    1. 코드를 읽고 firstName이라는 변수를 이해
    2. 초깃값이 “whitney”이므로 firstName이 string타입으로 인지
    3. firstName의 .length멤버를 함수처럼 호출하는 코드를 확인
    4. string의 .length 멤버는 함수가 아닌 숫자라는 오류를 표시.

### 2.1.2 오류 종류

- 구문 오류: ts가 js로 변환되는 것을 차단한 경우
    - ts가 이해할 수 없는 잘못된 구문을 감지할 때 발생한다. 물론 설정값을 변경하여 js코드를 얻을 수는 있지만, 예상과 상당히 다를 수 있기때문에 될수 있으면 구문오류를 수정하는것이 좋다.
        
        ```jsx
        // ts
        let let wat;
        
        // js로 컴파일후
        let let, wat;ㄴ
        ```
        
- 타입 오류: 타입 검사기에 따라 일치하지 않는 것이 감지된 경우
    - ts의 타입 검사기가 프로그램의 타입에서 오류를 감지했을 때 발생.
    - 오류가 발생했다고 해서 ts → js로의 변환되는것을 차단하지는 않는다. 하지만 코드가 실행되면 무언가 충돌하거나 예기치 않게 작동할 수 있다. 따라서 구문오류와 마찬가지로 js를 실행하기 전에 타입 오류를 수정하는것이 좋다.

## 2.2 할당 가능성

- ts는 변수의 초깃값을 읽고 해당 변수가 허용되는 타입을 결정한다. 이후 해당 변수에 새로운 값이 할당되면, 새롭게 할당된 값의 타입이 변수의 타입과 동일한지 확인한다.
- ts에서 함수 호출이나 변수에 값을 제공할 수 있는지 여부를 확인하는 것을 ***할당 가능성*** 이라고 한다.

### 2.2.1 할당 가능성 오류 이해하기

- ‘Type… is not assignable to type…’
    - 첫번째 Type은 변수에 할당하려고 시도하는값의 타입
    - 두번째 Type은 기존에 할당된 값의 타입.

## 2.3 타입 애너테이션

- ts로 작성된 변수의 초깃값이 없는경우 ts는 그 변수를 암묵적인 any타입으로 간주한다. 이렇게되면 ts의 타입 검사 목적을 쓸모없게 만든다.
- ts는 초깃값을 할당하지 않고도 변수의 타입을 선언할 수 있는 구문인 ***타입 애너테이션*** 을 제공한다.
    
    ```jsx
    // 타입 애너테이션 : 콜론(:)과 타입 이름을 차례대로 기재.
    let rocker: stirng;
    rocker = "Joan jett";
    ```
    
- 타입 애너테이션은 ts에만 존재하며 런타임 코드에 영향을 주지도 않고, 유효한 js 구문도 아니다.
- tsc 명령어를 실행해 ts → js로 컴파일하면 해당 코드가 삭제된다.
    
    ```jsx
    // js로 컴파일됨.
    let rocker;
    rocker = "Joan jett";
    ```
    

### 2.3.1 불필요한 타입 애너테이션

- 타입 애너테이션은 ts가 자체적으로 수집할 수 없는 정보를 ts에 제공할 수 있다.
- 타입을 즉시 유추할 수 있는 변수에도 타입 애너테이션을 사용할 수 있다
- 아래 코드에서 string 타입 애너테이션은 중복이다.( ts가 firstName이 string타입임을 유추할 수 있기때문)
    
    ```jsx
    let firstName : string = "Tina" 
    ```
    
- 변하지 않는 변수에는 타입 애너테이션을 추가하지 않기를 선호하는편.

## 2.4 타입 형태

- ts는 변수에 할당된 값이 원래 타입과 일치하는지 확인하는 것 이상으로 객체 사용과 관련된 문제도 알려준다.

```jsx
let rapper = "a";
rapper.push("1"); // Error : Property 'push' does not exist on type "string"
```

## 2.4.1 모듈

- js는 비교적 최근까지 서로 다른 파일에 작성된 코드를 공유하는 방법과 관련된 사양을 제공하지 않았다.
- ECMA스크립트 2015에는 파일 간에 가져오고 내보내는 구문을 표준화하기 위해 ECMA스크립트 모듈(ESM)이 추가되었다.
    - 모듈 : export 또는 import가 있는 파일
        
        ```jsx
        // a.ts
        export const shared = "Cher";
        ```
        
        ```jsx
        // b.ts
        export const shared = "Cher";
        ```
        
    - 스크립트 : 모듈이 아닌 파일
        
        ```jsx
        //a.ts
        const shared = "Cher" // Error: Cannot redeclare Block_scoped variable 'shared'
        ```
        
        ```jsx
        //b.ts
        const shared = "Cher" // Error: Cannot redeclare Block_scoped variable 'shared'
        ```
        
- ECMA스크립트 사양에 따라 export 또는 import 문 없이 파일을 모듈로 만들어야한다면 파일의 아무곳에나 export {};를 추가해 강제로 모듈이 되도록 만들수 있다.
- 타입스크립트는 기본적으로 모듈이 ESM구문으로 작성되었다고 가정하며 CommonJS파일의 import, export 형태는 인식하지 못한다.( tsconfig.json에서 설정하여 사용할수있다.)