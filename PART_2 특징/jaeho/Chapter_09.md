# CHAPTER 9 타입 제한자

## 9.1 top 타입

- 4장 ‘객체’에서 가능한 값이 없고, 접근 불가능한 타입을 설명하기 위해 bottom 타입 개념을 언급했습니다.
  - 그 반대 개념인 top 타입은 시스템에서 가능한 모든 값을 나타내는 타입입니다. 모든 타입은 top 타입에 할당할 수 있습니다.
- any
  - 모든 타입의 위치에 제공될 수 있다는 점에서 top 타입처럼 작동할 수 있습니다.
  - 타입스크립트가 해당 값에 대한 할당 가능성 또는 멤버에 대해 타입 검사를 수행하지 않도록 명시적으로 지시한다는 문제점을 갖습니다.
- unknown
  - 타입스크립트에서 unknown 타입은 진정한 top 타입입니다.
  - unknown 타입과 any 타입의 주요 차이점으로는 타입스크립트는 unknown 타입의 값을 훨씬 더 제한적으로 취급한다는 점입니다.
    - 타입스크립트는 unknown 타입 값의 속성에 직접 접근할 수 없습니다.
      - instanceof 나 typeof 또는 타입 어서션을 사용하는 것처럼 값의 타입이 제한된 경우 접근할 수 있습니다.
    - unknown 타입은 top 타입이 아닌 타입에는 할당할 수 없습니다.

## 9.2 타입 서술어

- 제한된 검사로 instanceof, typeof와 같은 방법을 직접 사용할 때는 괜찮지만, 로직을 함수로 감싸면 타입을 좁힐 수 없게 됩니다.
- 타입스크립트에는 인수가 특정 타입인지 여부를 나타내기 위해 boolean 값을 반환하는 함수를 위한 특별한 구문이 있습니다. 이를 `타입 서술어 type predicate`라고 부르며 `‘사용자 정의 타입 가드(user-defined type guard)’`라고도 부릅니다.
- 타입 서술어는 일반적으로 매개변수로 전달된 인수가 매개변수의 타입보다 더 구체적인 타입인지 여부를 나타내는 데 사용됩니다.
  - 타입 서술어의 반환 타입은 매개변수의 이름, is 키워드, 특정 타입으로 선언할 수 있습니다.
    ```jsx
    function isNumberOrString(value: unknown): value is number | string { .. }
    ```

## 9.3 타입 연산자

- keyof
  - 자바스크립트 객체는 일반적으로 string 타입인 동적값을 사용하여 검색된 멤버를 갖습니다.
  - 허용되는 키를 위한 리터럴 유니언 타입을 사용하는 방법이 있습니다. 이 경우 컨테이너 값에 존재하는 키만 적절하게 제한하는 것이 더 정확합니다. 하지만 인터페이스에 수십 개 이상의 멤버가 있다면 수동적인 작업이 번거롭습니다.
    ```jsx
    function getRating(ratings: Ratings, key: 'audience' | 'critic'): number { .. }
    ```
  - 타입스크립트에서는 기존에 존재하는 타입을 사용하고, 해당 타입에 허용되는 모든 키의 조합을 반환하는 keyof 연산자를 제공합니다.
    ```jsx
    function getRating(ratings: Ratings, key: keyof Radings): number { .. }
    ```
- typeof
  - 제공되는 값의 타입을 반환합니다.
  - 자바스크립트의 typeof 연산자는 타입에 대한 문자열 이름을 반환하는 런타임 연산입니다.
  - 타입스크립트의 typeof 연산자는 타입스크립트에서만 사용할 수 있으며 컴파일된 자바스크립트 코드에는 나타나지 않습니다.
  - keyof typeof
    - 값의 타입에 허용된 키를 간결하게 검색할 수 있습니다.
    - keyof 와 typeof 를 결합해서 사용하면 명시적 인터페이스 타입이 없는 객체에 허용된 키를 나타내는 타입에 대한 코드를 작성하고 업데이트하는 수고를 줄일 수 있습니다.
    ```jsx
    const ratings = {
    	imdb: 8.4,
    	metacritic: 82,
    };

    function logRating(key: keyof typeof ratings) {
    	console.log(ratings[key]);
    }

    logRating('imdb');
    logRating('invalid');
    // ERROR : Argument of type 'missing' is not assignable to parameter of type 'imdb' | 'metacritic'.
    ```

## 9.4 타입 어서션

- 타입스크립트 코드가 강력하게 타입화 (strongly typed) 될 때 가장 잘 작동합니다. 즉, 코드의 모든 값이 정확히 알려진 타입을 가지는 경우입니다. 타입스크립트의 타입 검사기가 복잡한 코드를 이해할 수 있도록 top 타입과 타입 가드 같은 기능을 제공합니다. 그러나 경우에 따라서는 코드가 어떻게 작동하는지 타입 시스템에 100% 정확하게 알리는 것이 불가능할 때도 있습니다.
  - 예를 들어 JSON.parse는 의도적으로 top 타입인 any를 반환합니다.
- 타입스크립트는 값의 타입에 대한 타입 시스템의 이해를 재정의하기 위한 구문으로 `타입 어서션 type assertion` 을 제공합니다.
- 오류 타입 어서션
  - 오류를 처리할 때 타입 어서션이 매우 유용할 수 있습니다. try 블록의 코드가 예상과 다른 객체를 예기치 않게 발생할 수 있기 때문에 catch 블록에서 포착된 오류가 어떤 타입인지 아는 것은 일반적으로 불가능합니다. 게다가 자바스크립트의 모법 사례는 항상 Error 클래스의 인스턴스를 발생시키지만, 일부 프로젝트에서는 문자열 리터럴 또는 다른 의외의 값을 발생시키기도 합니다.
    ```jsx
    try {
    } catch (error) {
    	console.log((error as Error).message);
    	// or
    	console.log(error instanceof Error ? error.message : error);
    }
    ```
- non-null 어서션
  - 실제로는 아니고 이론적으로만 null 또는 undefined를 포함할 수 있는 변수에서 null과 undefined를 제거할 때 타입 어서션을 주로 사용합니다.
  - non-null 어서션은 값을 반환하거나 존재하지 않는 경우 undefined를 반환하는 Map.get과 같은 API에서 특히 유용합니다.
    ```jsx
    const seasonCount = new Map([
    	["I Love Lucy", 6],
    	["The Golden Girls", 7],
    ]);

    const maybeValue = seasonCounts.get("I Love Lucy");
    			// ^? type is string | undefined

    const maybeValue = seasonCounts.get("I Love Lucy")!;
    			// ^? type is string
    ```
- 어서션 vs 선언
  - 변수 타입을 선언하기 위해 타입 애너테이션을 사용하는 것과 초깃값으로 변수 타입을 변경하기 위해 타입 어서션을 사용하는 것 사이에는 차이가 있습니다. 변수의 타입 애너테이션과 초깃값이 모두 있을 때, 타입스크립트의 타입 검사기는 변수의 타입 애너테이션에 대한 변수의 초깃값에 대해 할당 가능성 검사를 수행합니다. 그러나 타입 어서션은 타입스크립트에 타입 검사 중 일부를 건너뛰도록 명시적으로 지시합니다.
- 어서션 할당 가능성
  - 타입스크립트는 타입 중 하나가 다른 타입에 할당 가능한 경우에만 두 타입 간의 타입 어서션을 허용합니다.
  - 하나의 타입에서 값을 완전히 관련 없는 타입으로 전환해야 하는 경우 이중 타입 어서션을 사용합니다. 먼저 값을 any나 unknown 같은 top 타입으로 전환한 다음, 그 결과를 관련 없는 타입으로 전환합니다.
    ```jsx
    let myValue = "1337" as unknown as number;
    ```

## 9.5 const 어서션

- const 어서션은 배열, 원시 타입, 값, 별칭 등 모든 값을 상수로 취급해야 함을 나타내는 데 사용합니다.
  - 배열은 가변 배열이 아니라 읽기 전용 튜플로 취급됩니다.
  - 리터럴은 일반적인 원시 타입과 동등하지 않고 리터럴로 취급됩니다.
  - 객체의 속성은 읽기 전용으로 간주됩니다.
    - 값 리터럴에 const 어서션을 적용하면 해당 값 리터럴이 변경되지 않고 모든 멤버에 동일한 const 어서션 로직이 재귀적으로 적용됩니다.

## 9.6 마치며
