# CHAPTER 10 제네릭

- 타입스크립트는 제네릭을 사용해 타입 간의 관계를 알아냅니다.

## 10.1 제네릭 함수

- 제네릭 화살표 함수 구문은 .tsx 파일에서 JSX 구문과 충돌하므로 일부 제한이 있습니다. JSX 및 리액트 지원 구성과 해결 방법에 대해서는 13장 구성 옵션을 참조하세요.
- 명시적 제네릭 호출 타입
  ```jsx
  function logWrapper<Input>(callback: (input: Input) => void) {
    return (input: Input) => {
      console.log("Input", input);
      callback(input);
    };
  }

  logWrapper((input: string) => {
    console.log(input.length);
  });

  logWrapper <
    string >
    ((input) => {
      console.log(input.length);
    });

  logWrapper((input) => {
    console.log(input.length);
    // ERROR : 'input' is of type 'unknown'.
  });
  ```
- 다중 함수 타입 매개변수
  - 함수가 여러 개의 타입 매개변수를 선언하면 해당 함수에 대한 호출은 명시적으로 제네릭 타입을 모두 선언하지 않거나 모두 선언해 합니다. 타입스크립트는 아직 제네릭 호출 중 일부 타입 만을 유추하지는 못합니다.
    ```tsx
    function makePair<Key, Value>(key: Key, value: Value) {
      return { key, value };
    }

    makePair("abe", 123);
    makePair<string, number>("abc", 123);
    makePair<"abc", 123>("abc", 123);

    makePair<string>("abc", 123);
    // ERROR : Expected 2 type arguments, but got 1.
    ```

## 10.2 제네릭 인터페이스

- 유추된 제네릭 인터페이스 타입
  ```tsx
  interface LinkedNode<Value> {
    next?: LinkedNode<Value>
    value: Value;
  }

  function getLast<Value>(node: LinkedNode<Value>): Value {
    return node.next ? getLast(node.next) : node.value;
  }

  // 유추된 Value 타입 인수 : Date
  let lastDate = getLast({
    value: new Date("09-13-1993");
  });
  ```

## 10.3 제네릭 클래스

- 명시적 제네릭 클래스 타입
  - 생성자에 전달된 인수에서 클래스 타입 인수를 유추할 수 없는 경우에는 타입 인수의 기본값은 unknown이 됩니다.
    ```tsx
    class CurriedCallback<Input> {
      #callback: (input: Input) => void;

      constructor(callback: (input: Input) => void) {
        this.#callback = (input: Input) => {
          console.log("Input:", input);
          callback(input);
        };
      }

      call(input: Input) {
        this.#callback(input);
      }
    }

    new CurriedCallback((input: string) => {
      console.log(input.length);
    });

    new CurriedCallback<string>((input) => {
      console.log(input.length);
    });

    new CurriedCallback((input) => {
      console.log(input.length);
      // ERROR : 'input' is of type 'unknown'.
    });
    ```
- 제네릭 클래스 확장
  ```tsx
  class Quote<T> {
    lines: T;

    constructor(lines: T) {
      this.lines = lines;
    }
  }

  class AttributedQuote<Value> extends Quote<Value> {
    speaker: string;

    constructor(value: Value, speaker: string) {
      super(value);
      this.speaker = speaker;
    }
  }

  new AttributedQuote(
    "The road to success is always under construction",
    "Lily Tomlin"
  );
  ```
- 제네릭 인터페이스 구현
  ```tsx
  interface ActingCredit<Role> {
    role: Role;
  }

  class MoviePart implements ActingCredit<string> {
    role: string;
    speaking: boolean;

    constructor(role: string, speaking: boolean) {
      this.role = role;
      this.speaking = speaking;
    }
  }

  const part = new MoviePart("Miranda Priestly", true);
  part.role; // string
  ```
- 메서드 제네릭
  - 클래스 메서드는 클래스 인스턴스와 별개로 자체 제네릭 타입을 선언할 수 있습니다. 제네릭 클래스 메서드에 대한 각각의 호출은 각 타입 매개변수에 대해 다른 타입 인수를 갖습니다.
    ```tsx
    class CreatePairFactory<Key> {
      key: Key;

      constructor(key: Key) {
        this.key = key;
      }

      createPair<Value>(value: Value) {
        return { key: this.key, value };
      }
    }

    const factory = new CreatePairFactory("role");

    const numberPair = factory.createPair(10);
    // { key: string, value: number }
    const stringPair = factory.createPair("Sophie");
    // { key: string, value: string }
    ```
- 정적 클래스 제네릭
  - 클래스의 정적 멤버는 인스턴스 멤버와 구별되고 클래스의 특정 인스턴스와 연결되어 있지 않습니다. 클래스의 정적 멤버는 클래스 인스턴스에 접근할 수 없거나 타입 정보를 지정할 수 없습니다. 따라서 정적 클래스 메서드는 자체 타입 매개변수를 선언할 수 있지만, 클래스에 선언된 어떤 타입 매개변수에도 접근할 수 없습니다.
    ```tsx
    class BothLogger<OnInstance> {
      instanceLog(value: OnInstance) {
        console.log(value);
        return value;
      }

      static staticLog<OnStatic>(value: OnStatic) {
        let fromInstance: OnInstance;
        // ERROR : Static members cannot reference class type arguments.
        console.log(value);
        return value;
      }
    }
    ```

## 10.4 제네릭 타입 별칭

- 제네릭 판별된 유니언
  ```tsx
  type Result<Data> = FailureResult | SuccessFulResult<Data>;

  interface FailureResult {
    error: Error;
    succeeded: false;
  }

  interface SuccessfulResult<Data> {
    data: Data;
    succeeded: true;
  }

  function handleResult(result: Result<string>) {
    if (reesult.succeeded) {
      // result: SuccessfulResult<string> 의 타입
    } else {
      // result: FailureResult 의 타입
    }
    result.data;
    // ERROR : Property 'data' does not exist on type 'Result<string>'.
    // ERROR : Property 'data' does not exist on type 'FailureResult'.
  }
  ```

## 10.5 제네릭 제한자

- 제네릭 기본값
  - 기본값은 타입 인수가 명시적으로 선언되지 않고 유추할 수 없는 모든 후속 타입에 사용됩니다.
  - 모든 기본 타입 매개변수는 기본 함수 매개변수처럼 선언 목록의 제일 마지막에 와야 합니다. 기본값이 없는 제네릭 타입은 기본값이 있는 제네릭 타입 뒤에 오면 안 됩니다.
    ```tsx
    interface Quote<T = string> {
      value: T;
    }

    let explicit: Quote<number> = { value: 123 };
    let implicit: Quote = { value: "Be yourself." };
    let mismatch: Quote = { value: 123 };
    // ERROR : Type 'number' is not assignable to type 'string'.
    ```

## 10.6 제한된 제네릭 타입

- 타입스크립트는 타입 매개변수가 타입을 확장해야 한다고 선언할 수 있으며 별칭 타입에만 허용되는 작업입니다. 타입 매개변수를 제한하는 구문은 매개변수 이름 뒤에 extends 키워드를 배치하고 그 뒤에 이를 제한할 타입을 배치합니다.
  ```tsx
  interface WithLength {
    length: number;
  }

  function logWithLength<T extends WithLength>(input: T) {
    console.log(`Length: ${input.length}`);
    return input;
  }

  logWithLength("No one can ifure out your worth but you."); // string
  logWithLength([false, true]); // boolean[]
  logWithLength({ length: 123 }); // { length: number }

  logWithLength(new Date());
  // ERROR : Argument of type 'Date' is not assignable to parameter of type 'WithLength'.
  ```
- keyof와 제한된 타입 매개변수
  ```tsx
  function get<T, Key extends keyof T>(container: T, key: Key) {
    return container[key];
  }

  const roles = {
    favorite: "Fargo",
    others: ["Almost Famous", "Burn After Reading", "Nomadland"],
  };

  const favorite = get(roles, "favorite"); // string
  const others = get(roles, "others"); // string[]

  const missing = get(roles, "extras");
  // ERROR : Argument of type '"extras"' is not assignable to parameter of type '"favorite" | "others"'.
  ```
  - 타입 매개변수로 T만 제공되고 key 매개변수가 모든 keyof T일 수 있는 경우라면 반환 타입은 Container에 있는 모든 속성값에 대한 유니언 타입이 됩니다. 이렇게 구체적이지 않은 함수 선언은 각 호출이 타입 인수를 통해 특정 key를 가질 수 있음을 타입스크립트에 나타낼 수 없습니다.
    ```tsx
    function get<T>(container: T, key: keyof T) {
      return container[key];
    }

    const roles = {
      favorite: "Fargo",
      others: ["Almost Famous", "Burn After Reading", "Nomadland"],
    };

    const found = get(roles, "favorite");
    // string | string[]
    ```

## 10.7 Promise

- Promise 생성
  - 결과적으로 값을 resolve하려는 Promise를 만들려면 Promise의 타입 인수를 명시적으로 선언해야 합니다. 타입스크립트는 명시적 제네릭 타입 인수가 없다면 기본적으로 매개변수 타입을 unknown으로 가정합니다. Promise 생성자에 타입 인수를 명시적으로 제공하면 타입스크립트가 결과로서 Promise 인스턴스의 resolve된 타입을 이해할 수 있습니다.
    ```tsx
    // Promise<unknown>
    const resolvesUnknown = new Promise((resolve) => {
      setTimeout(() => resolve("Done!"), 1000);
    });

    // Promise<string>
    const resolvesString = new Promise<string>((resolve) => {
      setTimeout(() => resolve("Done!"), 1000);
    });
    ```
  - Promise의 제네릭 .then 메서드는 반환되는 Promise의 resolve된 값을 나타내는 새로운 타입 매개변수를 받습니다.
    ```tsx
    // Promise<string>
    const textEventually = new Promise<string>((resolve) => {
      setTimeout(() => resolve("Done!"), 1000);
    });

    // Promise<number>
    const lengthEventually = textEventually.then((text) => text.length);
    ```
- async 함수
  - 자바스크립트에서 async 함수에 따라서 반환된 값이 Thenable(.then() 메서드가 있는 객체, 실제로는 거의 항상 Promise)이 아닌 경우, Promise.resolve가 호출된 것처럼 Promise로 래핑됩니다.
    ```tsx
    // (text: string) => Promise<number>
    async function lengthAfterSecond(text: string) {
      await new Promise((resolve) => setTimeout(resolve, 1000));
      return text.length;
    }
    ```

## 10.8 제네릭 올바르게 사용하기

- 제네릭 황금률
  - 함수에 타입 매개변수가 필요한지 여부를 판단할 수 있는 간단하고 빠른 방법은 타입 매개변수가 최소 두 번 이상 사용되었는지 확인하는 것입니다.
  - 댄 밴더캄의 ‘이펙티브 타입스크립트’는 ‘제네릭 황금률’을 설명하며 제네릭으로 작업할 때 유용하고 휼륭한 팁도 소개합니다.
- 제네릭 명명 규칙
  - 타입스크립트를 포함한 많은 언어가 지키는 타입 매개변수에 대한 표준 명명 규칙은 기본적으로 첫 번째 타입 인수로 T를 사용하고, 후속 타입 매개변수가 존재하면 U, V 등을 사용하는 것입니다.
  - 타입 인수가 어떻게 사용되어야 하는지 맥락과 관련된 정보가 알려진 경우 명명 규칙은 경우에 따라 해당 용어의 첫 글자를 사용하는 것으로 확장됩니다.
  - 구조체가 여러 개의 타입 매개변수를 갖거나 단일 타입 인수의 목적이 명확하지 않을 때마다 단일 문자 약어 대신 가독성을 위해 완전히 작성된 이름을 사용하는 것이 좋습니다.

## 10.9 마치며
