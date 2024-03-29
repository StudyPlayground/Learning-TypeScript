- 자바스크립트는 10일이라는 짧은 기간만에 설계되었고, 처음나왔을 때에는 다른 언어에 비해 특인한 특성과 드러난 결점을 지적받았다.
- 하지만 시간이 지나면서, 자바스크립트의 기반이 되는 언어 사양인 ECMA 스크립트의 새로운 버전에 신기능을 포함해 매년 출시했고, 브라우저, 임베디드 애플리케이션, 서버 런타임을 포함한 다양한 환경에서 새로운 버전과 이전 버전을 호환 유지했다.
- 자바스크립트는 장점이 많은 유연한 언어이다. 하지만..

### 1.2) 바닐라 자바스크립트의 함정

순수 자바스크립트의 자유도는 매우 높다. 그 만큼 주의를 기울여야 할 점도 많다.

- 코드 파악 어려움: 컴파일 언어와 달리, 자바스크립트는 충돌 가능성을 먼저 확인하지 않고 코드를 실행하는 동적 타입 언어이다. 즉, 예측이 어렵다.
- 부족한 문서: 자바스크립트의 언어 사양에는 함수의 매개 변수, 함수 반환, 변수 등 다른 구성 요소의 의미를 설명하는 표준화된 내용이 없다.
    
    따라서 많은 개발자가 블록 주석으로 함수와 변수를 설명하는 JSDoc 표준을 채택했다. 하지만 주석이기 때문에 실제로 사용할 때 어려움이 많았다. 
    
    - jsDocs
        
        형식화된 함수와 변수 코드 바로 위해 문서 주석을 작성하는 방식이다. 
        
        - 일반 주석이기 때문에, 작성한다고 해도 설명이 코드가 잘못되는 것을 막을 수 없다.
        - 이전에는 정확했더라도 기능 추가, 리팩터링 중에 생긴 변경 사항과 관련된 현재 유효하지 않는 주석을 찾기 어렵다.
        - 복잡한 객체를 설명할 때에는 다루기 어렵고 장황해 타입과 그 관계를 정의하려면 다수의 독립형 주석이 필요하다.
        
        타입스크립트가 제공하는 주석 유형의 타입스크립트 `@ts-check`를 vscode에서 주석으로 파일의 시작 부분에 추가하고 jsDoc형식의 주석을 작성하면 내장 TypeScript가 타입 및 에러 체크가 가능하다고 한다. 
        
- 자바스크립트는 타입을 식별하는 내장된 방법을 제공하지 않아 코드베이스에 대한 대규모 변경을 자동화하거나 통찰력을 얻기가 매우 어렵다.
    
    <aside>
    💡 VSCode같은 최신 IDE가 자동화된 리팩터링과 같은 개발 도구를 제공한다고 주장할 수 있지만, 그러한 개발 도구는 자바스크립트의 다양한 기능을 위해 내부적으로 타입스크립트 혹은 이에 상응하는 것을 사용한다. 또한 자바스크립트 코드에서 잘 정의된 타입스크립트 코드만큼 안정적이거나 강력하지 않다.
    
    </aside>
    

## 1.3 타입스크립트

타입스크립트는 네 가지로 설명이 가능하다. 

- **프로그래밍 언어:** 자바스크립트의 모든 구문과 타입을 정의하고 사용하기 위한 새로운 타입스크립트 고유 구문이 포함된 언어(JavaScript의 상위 집합, 타입 구문이 있는 JavaScript)
- **타입 검사기:** 자바스크립트 타입스크립트로 작성된 일련의 파일에서 생성된 모든 구성 요소(변수, 함수 등)을 이해하고, 잘못 구성된 부분을 알려주는 프로그램
- **컴파일러(JS→ TS):** 타입 검사기를 실행하고 문제를 보고한 이후 대응되는 자바스크립트 코드를 생성하는 프로그램. Javascript가 지원되는 모든 곳에서 실행된다.(브라우저, Node, Deno 등)
- **언어 서비스:** 타입 검사기를 사용해 비주얼 스튜디오 코드와 같은 편집기에 개발자에게 유틸리티 제공법을 알려주는 프로그램

## 1.4 타입스크립트 플레이그라운드

- 제한을 통한 자유: 자바스크립트가 가진 근시안적 자유(틀린 매개변수 타입 등)을 제한해서 잘못된 사용을 막는다.
- 정확한 문서화: 구문을 적용해 객체의 형태를 설명하고, 우수하고 강력한 시스템을 이용해 객체가 어떻게 보이는 지 설명한다.
- 강력한 개발자 도구: 편집기에서 해당 타입에 대한 자동완성 제공
- 구문 컴파일: 타입스크립트 컴파일러가 TypeScript → JavaScript로 컴파일한다. 최신 자바스크립트 구문이나 이전 ECMA스크립트에 상응하는 코드로 컴파일 할 수도 있다.

<aside>
💡 많은 자바스크립트 프로젝트는 소스 코드를 실행할 수 있는 자바스크립트로 변환하기 위해 타입스크립트의 자체 컴파일러 대신 바벨과 같은 전용 변환기를 사용한다.

</aside>

---

## 1.5 로컬에서 시작하기

### 1.5.2 로컬에서 실행하기

Node.js가 설치되어 있으면 타입스크립트를 실행할 수 있다.

`tsconfig.json` 구성 파일을 생성하려면 `tsc --init` 명령어를 사용한다. tsconfig.json 파일은 타입스크립트가 코드를 분석할 때 사용하는 설정을 선언한다.

tsc를 실행해 모든 파일을 컴파일하도록 지시할 수 있고, 타입스크립트가 모든 구성 옵션에 대해 tsconfig.json을 참조할 수 있다는 것이다.

타입스크립트가 ts 파일을 참조할 수 있도록 설정하고, (`tsc index.ts`) 에러를 포함한 ts 파일을 컴파일 하면 에러가 출력된다. 

이때 타입스크립트를 정상적으로 작동시키기 위해 코드를 수정하기 전에, 자바스크립트로 컴파일했다. 타입스크립트 컴파일러는 타입 오류와 상관 없이 입력파일로부터 자바스크립트를 계속 생성한다. 

### 1.5.2 편집기 기능

tsconfig.json 파일을 생성할 때의 또 다른 이점은 편집기에서 특정 폴더를 열었을 때, 편집기가 해당 폴더를 타입스크립트 프로젝트로 인식한다. 예를 들어, vscode에서 폴더를 열면 타입스크립트 코드를 분석하는데 사용하는 설정은 해당 폴더의 tsconfig.json을 따르게 된다. 코드 유틸리티를 제공한다. 

<aside>
💡 vsCode는 타입스크립트를 지원하고 자체적으로 타입스크립트로 빌드된다. 물론 대부분의 최신 편집기는 타입스크립트 지원 기능을 내장하고 있거나 플러그인을 통해 사용할 수 있다.

</aside>

## 1.6 타입스크립트에 대한 오해

- **잘못된 코드 해결책**

타입스크립트는 자바스크립트 코드를 구조화하는데 도움이 되지만, 타입 안정성 강화를 제외하고는 해당 구조가 어떻게 보여야 되는지 규정하지 않는다. 즉, 독단적인 프레임워크가 아닌 프로그래밍 언어라는 것이다. **자바스크립트로 할 수 있는 것은 타입스크립트로도 할 수 있다.**

- **자바스크립트로의 확장**

타입스크립트의 설계 목표는 다음과 같다

- 현재와 미래의 ECMA 스크립트 제안에 맞춘다
- 모든 자바스크립트 코드의 런타임 동작을 유지한다.

이처럼 타입스크립트는 자바스크립트 작동방식을 전혀 변경하지 않는다. 타입스크립트 개발자들은 자바스크립트에 추가되거나 자바스크립트와 충돌할 수 있는 새로운 코드 기능을 타입스크립트에 추가하지 않도록 노력한다.

- **자바스크립트보다 느림**

타입스크립트가 코드에 적용하는 유일한 변경사항은 지금은 없어진 익스플로러 11과 같이 오래된 런타임 환경을 지원하기 위해 이전 버전의 자바스크립트로 코드를 컴파일하도록 요청하는 경우 이다. 그렇지만 **운영 프레임워크 대다수는 타입스크립트의 컴파일러를 전혀 사용하지 않는다. 대신 트렌스파일을 위한 별도의 도구를 사용하고, 타입스크립트는 타입 검사용으로만 사용한다.**

타입스크립트는 코드를 빌드하는데 시간이 더 걸린다. 타입스크립트 코드는 브라우저나 Node.js와 같은 환경에서 실행되기 전에 자바스크립트로 컴파일되어야 한다. 

- **진화가 끝남**

웹의 진화가 끝나지 않은 것 처럼, 타입스크립트도 계속 발전중이다.
