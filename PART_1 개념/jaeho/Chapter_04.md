# CHAPTER 4 객체

## 4.1 객체 타입

- 객체 타입은 타입스크립트가 자바스크립트 코드를 이해하는 방법에 대한 핵심 개념입니다. null과 undefined를 제외한 모든 값은 그 값에 대한 실제 타입의 멤버 집합을 가지므로 타입스크립트는 모든 값의 타입을 확인하기 위해 객체 타입을 이해해야 합니다.
- 객체 타입 작성하는 방법
  ```tsx
  // 객체 타입 선언
  let poetLater: {
    born: number;
    name: string;
  };

  poetLater = {
    born: 1935,
    name: "Mary Oliver",
  };

  // 별칭 객체 타입 (interface 권장)
  interface Poet {
    born: number;
    name: string;
  }

  let poetLater: Poet;
  poetLater = {
    born: 1935,
    name: "Mary Oliver",
  };
  ```

## 4.2 구조적 타이핑

- 타입스크립트의 타입 시스템은 구조적으로 타입화되어 있습니다.
  - 매개변수나 변수가 특정 객체 타입으로 선언되면 타입스크립트에 어떤 객체를 사용하든 해당 속성이 있어야 한다고 말해야 합니다.
- 구조적 타이핑과 덕 타이핑
  - 타입스크립트의 타입 검사기에서 구조적 타이핑은 정적 시스템이 타입을 검사하는 경우입니다.
  - 덕 타이핑은 런타임에서 사용될 때까지 객체 타입을 검사하지 않는 것을 말합니다.
- 객체 타입으로 애너테이션된 위치에 값을 제공할 때 타입스크립트는 값을 해당 객체 타입에 할당할 수 있는지 확인합니다.
- 변수가 객체 타입으로 선언되고, 초깃값에 객체 타입에서 정의된 것보다 많은 필드가 있다면 타입스크립트에서 타입 오류가 발생합니다.
  ```tsx
  type ObjType = {
    name: string;
    born: number;
  };

  const obj: ObjType = {
    // ERROR
    name: "Mary Oliver",
    born: 1935,
    activity: "walking",
  };
  ```
  - 초과 속성 검사는 객체 타입으로 선언된 위치에서 생성되는 객체 리터럴에 대해서만 일어납니다. 기존 객체 리터럴을 제공하면 초과 속성 검사를 우회합니다.
    ```tsx
    type ObjType = {
      name: string;
      born: number;
    };

    const obj = {
      name: "Mary Oliver",
      born: 1935,
      activity: "walking",
    };

    const obj2: ObjType = obj;
    ```
- 중첩된 객체 타입을 고유한 타입 이름으로 바꿔서 사용하면 코드와 오류 메시지가 더 읽기 쉬워집니다.
- 타입의 속성 애너테이션에서 `:` 앞에 `?`를 추가하면 선택적 속성임을 나타낼 수 있습니다.
  - 선택적 속성과 `undefined`를 포함한 유니언 타입의 속성 사이에는 차이가 있음을 명심하세요. 필수로 선언된 속성과 `| undefined`는 그 값이 `undefined`일지라도 반드시 존재해야 합니다.
    ```tsx
    type Writer = {
    	author: string | undefined;
    	editor: string;
    }

    const hasRequired: Writer = {
    	author: undefined;
    };

    const missingRequired: Writer = {}  // ERROR : author속성이 필요합니다.
    ```

## 4.3 객체 타입 유니언

- 변수에 여러 객체 타입 중 하나가 될 수 있는 초깃값의 주어지면 타입스크립트는 해당 타입을 객체 타입 유니언으로 유추합니다.
  ```tsx
  const poem = Math.random() > 0.5
  	? { name: "The Double Image", pages: 7 }
  	: { name: "Her Kind", rhymes: true }

  // 타입은 아래와 같습니다.
  {
  	name: string;
  	pages: number;
  	rhymes?: undefined;
  }
  |
  {
  	name: string;
  	pages?: undefined;
  	rhymes: boolean;
  }

  poem.name // string;
  poem.pages // number | undefined
  poem.rhymes // boolean | undefined
  ```
- 객체 타입의 조합을 명시하면 객체 타입을 더 명확히 정의할 수 있습니다.
  - 특히 값의 타입이 객체 타입으로 구성된 유니언이라면 타입스크립트의 타입 시스템은 이런 모든 유니언 타입에 존재하는 속성에 대한 접근만 허용합니다.
  - 잠재적으로 존재하지 않는 객체의 멤버에 대한 접근을 제한하면 코드의 안전을 지킬 수 있습니다.
  - 리터럴 타입이나 원시 타입 모두, 혹은 둘 중 하나로 이루어진 유니언 타입에서 모든 타입에 존재하지 않은 속성에 접근하기 위해 타입을 좁혀야 하는 것처럼 객체 타입 유니언도 타입을 좁혀야 합니다.
    ```tsx
    type PoemWithPages = {
      name: string;
      pages: number;
    };

    type PoemWithRhymes = {
      name: string;
      rhymes: boolean;
    };

    type Pem = PoemWithPages | PoemWithRhymes;

    const poem =
      Math.random() > 0.5
        ? { name: "The Double Image", pages: 7 }
        : { name: "Her Kind", rhymes: true };

    poem.name; // OK
    poem.pages; // ERROR : 해당 속성이 없습니다.
    poem.rhymes; // ERROR : 해당 속성이 없습니다.
    ```
- 타입 검사기가 유니언 타입 값에 특정 속성이 포함된 경우에만 코드 영역을 실행할 수 있음을 알게 되면, 값의 타입을 해당 속성을 포함하는 구성 요소로만 좁힙니다.
  - 타입 스크립트는 `if (poem.pages)`와 같은 형식으로 참 여부를 확인하는 것을 허용하지 않습니다.
  ```tsx
  if ("pages" in poem) {
  	poem.pages;
  } else {
  	poem.rhymes;
  }

  if (poem.pages) { ... } // ERROR : 해당 속성이 없습니다.
  ```
- 객체의 속성이 객체의 형태를 나타내도록 하는 타입 형태를 `판별된 유니언`이라고 부르고, 객체의 타입을 가리키는 속성이 `판별값`입니다.
  - 타입스크립트는 코드에서 판별 속성르 사용해 타입 내로잉을 수행합니다.
  ```tsx
  type PoemWithPages = {
    name: string;
    pages: number;
    type: "pages";
  };

  type PoemWithRhymes = {
    name: string;
    rhymes: boolean;
    type: "rhymes";
  };

  type Poem = PoemWithPages | PoemWithRhymes;

  const poem: Poem =
    Math.random() > 0.5
      ? { name: "The Double Image", pages: 7 }
      : { name: "Her Kind", rhymes: true };

  if (poem.type === "pages") {
    poem.pages;
  }
  ```

## 4.4 교차 타입

- `& 교차 타입(intersection type)`은 일반적으로 여러 기존 객체 타입을 별칭 객체 타입으로 결합해 새로운 타입을 생성합니다.
  - 교차 타입은 유니언 타입과 결합할 수 있으며, 이는 하나의 타입으로 판별된 유니언 타입을 설명하는 데 유용합니다.
    ```tsx
    type ShortPoem = { author: string } & (
      | { kigo: string; type: "haiku" }
      | { meter: number; type: "villanelle" }
    );
    ```
- 교차 타입은 유용한 개념이지만, 여러분 스스로나 타입스크립트 컴파일러를 혼동시키는 방식으로 사용하기 쉽습니다. 유니언 타입과 결합하는 것처럼 복잡한 교차 타입을 만들게 되면 할당 가능성 오류 메시지는 읽기 어려워집니다.
- 교차 타입은 잘못 사용하기 쉽고 불가능한 타입(`never 타입`)을 생성합니다.

## 4.5 마치며
