# CHAPTER 8 클래스

## 8.1 클래스 메서드

- 타입스크립트는 일반 함수를 이해하는 것과 동일한 방식으로 메서드를 이해합니다.
- 클래스 생성자는 매개변수와 관련하여 전형적인 클래스 메서드처럼 취급됩니다.

## 8.2 클래스 속성

- 클래스의 속성은 타입 애너테이션 또는 생성자 내의 할당을 통해 타입을 추론할 수 있습니다.
  ```jsx
  class Test {
    name: string;
    key = 123;
    // key is number type
    value;
    // value is string type

    constructor() {
      this.name = 789;
      // ERROR : Type 'number' is not assignable to type 'string'.
      this.value = "message";
    }
  }
  ```
- 함수 속성
  - 메서드 접근 방식은 함수를 클래스 프로토타입에 할당하므로 모든 클래스 인스턴스는 동일한 함수 정의를 사용합니다.
    ```jsx
    class WithMethod {
      myMethod() {}
    }

    new WithMethod().myMethod === new WithMethod().myMethod;
    // true
    ```
  - 값이 함수인 속성을 선언하는 방식도 있습니다. 이렇게 하면 클래스의 인스턴스당 새로운 함수가 생성되며, 항상 클래스 인스턴스를 가리켜야 하는 화살표 함수에서 this 스코프를 사용하면 클래스 인스턴스당 새로운 함수를 생성하는 시간과 메모리 비용 측면에서 유용할 수 있습니다.
    ```jsx
    class WithProperty {
      myProperty: () => {};
    }

    new WithMethod().myProperty === new WithMethod().myProperty;
    // false
    ```
- 초기화 검사
  - 엄격한 컴파일러 설정이 활성화된 상태에서 타입스크립트는 undefined 타입으로 선언된 각 속성이 생성자에서 할당되었는지 확인합니다.
  - 엄격한 초기화 검사가 없다면, 비록 타입 시스템이 undefined 값에 접근할 수 없다고 말할지라도 클래스 인스턴스는 undefined 값에 접근할 수 있습니다.
  - 다음 예제는 엄격한 초기화 검사가 수행되지 않으면 올바르게 컴파일되지만, 결과 자바스크립트는 런타임 시 문제가 발생합니다.
    ```jsx
    class MissingInitializer {
      property: string;
    }

    new MissingInitializer().property.length;
    // ERROR : Cannot read properties of undefined (reading 'length')
    ```
  - 엄격한 초기화 검사를 적용하면 안 되는 속성인 경우에는 이름 뒤에 `!`를 추가해 검사를 비활성화하도록 설정합니다. 이렇게 하면 타입스크립트에 속성이 처음 사용되기 전에 undefined 값이 할당됩니다.
    - `!` 어서션을 추가하고 속성에 대한 타입 안정성을 줄이는 대신 클래스를 리팩터링해서 어서션이 더 이상 필요하지 않도록 하세요.
    ```jsx
    class ActivitiesQueue {
        pending!: string[];

        initialize(pending: string[]) {
            this.pending = pending;
        }

        next() {
            return this.pending.pop();
        }
    }

    const activities = new ActivitiesQueue();
    activities.initialize(['eat', 'sleep', 'learn']);
    activities.next();
    ```
- 선택적 속성
  - 인터페이스와 마찬가지로 클래스는 선언된 속성 이름 뒤에 `?`를 추가해 속성을 옵션으로 선언합니다.
  - 선택적 속성은 `| undefined`를 포함하는 유니언 타입과 겅의 동일하게 작동합니다.
    ```jsx
    class MissingInitializer {
        property?: string;
    }

    new MissingInitializer().property?.length;
    // OK
    new MissingInitializer().property.length;
    // ERROR : Object is possibly 'undefined'.
    ```
- 읽기 전용 속성
  - 인터페이스와 마찬가지로 클래스도 선언된 속성 이름 앞에 readonly 키워드를 추가해 속성을 읽기 전용으로 선언합니다.
  - readonly로 선언된 속성은 선언된 위치 또는 생성자에서 초깃값만 할당할 수 있습니다. 클래스 내의 메서드를 포함한 다른 모든 위치에서 속성은 읽을 수만 있고, 쓸 수는 없습니다.
  - 원시 타입의 초깃값을 갖는 readonly로 선언된 속성은 더 넓은 윈싯값이 아니라 값의 타입이 가능한 한 좁혀진 리터럴 타입으로 유추됩니다. (const 변수가 let 변수보다 더 좁은 타입을 갖는 것과 유사합니다.)
    ```jsx
    class Test {
        readonly explicit: string = "explicit";
        readonly implicit = "implicit";

        constructor() {
            this.explicit = "another explicit";
            this.implicit = "another implicit";
            // ERROR : Type '"another implicit"' is not assignable to type '"implicit"'.
        }
    }

    const test = new Test();
    test.explicit; // type : string
    test.implicit; // type : "implicit"
    ```

## 8.3 타입으로서의 클래스

- 타입 시스템에서 클래스는 클래스 선언이 런타임 값(클래스 자체)과 타입 애너테이션에서 사용할 수 있는 타입을 모두 생성한다는 점에서 상대적으로 독특합니다.
- 타입스크립트는 클래스의 동일한 멤버를 모두 포함하는 모든 객체 타입을 클래스에 할당할 수 있는 것으로 간주합니다. 타입스크립트의 구조적 타이핑이 선언되는 방식이 아니라 객체의 형태만 고려하기 때문입니다.
  ```jsx
  class SchoolBus {
    getAbilities() {
      return ["magic", "shapeshifting"];
    }
  }

  function WithSchoolBus(bus: SchoolBus) {
    console.log(bus.getAbilities());
  }

  WithSchoolBus(new SchoolBus());

  WithSchoolBus({
    getAbilities: () => ["transmogrification"],
  });

  WithSchoolBus({
    getAbilities: () => 123,
    // ERROR : Type 'number' is not assignable to type 'string[]'.
  });
  ```

## 8.4 클래스와 인터페이스

- 타입스크립트는 클래스 이름 뒤에 implements 키워드와 인터페이스 이름을 추가함으로써 클래스의 해당 인스턴스가 인터페이스를 준수한다고 선언할 수 있습니다.
- 하지만 implements에 인터페이스를 작성했다고 클래스의 메서드 또는 속성 타입을 유추하지 않습니다. 따라서 클래스를 사용하는 방식대로 타입을 작성해야합니다.
  - 인터페이스를 구현하는 것은 순전히 안정성 검사를 위해서 입니다. 모든 인터페이스 멤버를 클래스 정의로 복사하지 않습니다. 대신 인터페이스를 구현하면 클래스 인스턴스가 사용되는 곳에서 나중에 타입 검사기로 신호를 보내고 클래스 정의에서 표면적인 타입 오류가 발생합니다. 변수에 초깃값이 있더라도 타입 애너테이션을 추가하는 것과 용도가 비슷합니다.
- 다중 인터페이스 구현도 가능하지만, 실제로 클래스가 한 번에 두 인터페이스를 구현할 수 없도록 정의하는 인터페이스가 있지 않도록 주의합니다.

## 8.5 클래스 확장

- 할당 가능성 확장
  - 파생 인터페이스가 기본 인터페이스를 확장하는 것과 마찬가지로 하위 클래스도 기본 클래스의 멤버를 상속합니다. 하위 클래스의 인스턴스는 기본 클래스의 모든 멤버를 가지므로 기본 클래스의 인스턴스가 필요한 모든 곳에서 사용할 수 있습니다. 만약 기본 클래스에 하위 클래스가 가지고 있는 모든 멤버가 없으면 더 구체적인 하위 클래스가 필요할 때 사용할 수 없습니다.
  - 타입스크립트의 구조적 타입에 따라 하위 클래스의 모든 멤버가 동일한 타입의 기본 클래스에 이미 존재하는 경우 기본 클래스의 인스턴스를 하위 클래스 대신 사용할 수 있습니다.
    ```jsx
    class Lesson {
        subject: string;

        constructor(subject: string) {
            this.subject = subject;
        }
    }

    class OnlineLesson extends Lesson {
        url: string;

        constructor(subject: string, url: string) {
            super(subject);
            this.url = url;
        }
    }

    class OfflineLesson extends Lesson {
        location?: string;
    }

    let lesson: Lesson;
    lesson = new Lesson("coding");
    lesson = new OnlineLesson("coding", "oreilly.com");

    let online: OnlineLesson;
    online = new OnlineLesson("coding", "oreilly.com");
    online = new Lesson("coding");
    // ERROR : Property 'url' is missing in type 'Lesson' but required in type 'OnlineLesson'.

    let offLine: OfflineLesson;
    offLine = new OfflineLesson("coding");
    offLine = new Lesson("coding");
    ```
- 재정의된 생성자
  - 자체 생성자가 없는 하위 클래스는 암묵적으로 기본 클래스의 생성자를 사용합니다.
  - 자바스크립트에서 하위 클래스가 자체 생성자를 선언하면 super 키워드를 통해 기본 클래스 생성자를 호출해야 합니다. 하위 클래스 생성자는 기본 클래스에서의 필요 여부와 상관없이 모든 매개변수를 선언할 수 있습니다. 타입스크립트의 타입 검사기는 기본 클래스 생성자를 호출할 때 올바른 매개변수를 사용하는지 확인합니다.
- 재정의된 메서드
  - 하위 클래스는 기본 클래스와 구조적으로 일치해야 합니다.
  - 기본 클래스를 사용하는 모든 곳에 하위 클래스를 사용할 수 있으므로 새 메서드의 타입도 기본 메서드 대신 사용할 수 있어야 합니다.
- 재정의된 속성
  - 재정의되니 메서드와 마찬가지로 하위 클래스는 기본 클래스와 구조적으로 일치해야 합니다.
  - 속성을 다시 선언하는 대부분의 하위 클래스는 해당 속성을 유니언 타입의 더 구체적인 하위 집합으로 만들거나 기본 클래스 속성 타입에서 확장되는 타입으로 만듭니다.
    ```jsx
    class Parent {
      property: string = "parent";
      method(value1: boolean, value2: number) {
        return value1 ? value2 : "false";
      }
    }

    class Child extends Parent {
      property: string = "child";

      method(value1: boolean) {
        return value1;
        // Property 'method' in type 'Child' is not assignable to the same property in base type 'Parent'.
        // Type '(value1: boolean) => boolean' is not assignable to type '(value1: boolean, value2: number) => number | "false"'.
        // Type 'boolean' is not assignable to type 'number | "false"'.
      }
    }
    ```

## 8.6 추상 클래스

- 일부 메서드의 구현을 선언하지 않고, 대신 하위 클래스가 해당 메서드를 제공할 것을 예상하고 기본 클래스를 만드는 방법이 유용할 수 있습니다. 추상화하려는 클래스 이름과 메서드 앞에 타입스크립트의 `abstract` 키워드를 추가합니다. 이러한 추상화 메서드 선언은 추상화 기본 클래스에서 메서드의 본문을 제공하는 것을 건너뛰고, 대신 인터페이스와 동일한 방식으로 선언됩니다.

## 8.7 멤버 접근성

- 타입스크립트의 클래스 지원은 자바스크립트의 # 프라이버시보다 먼저 만들어졌습니다.
- 타입스크립트의 멤버 접근성은 클래스 멤버의 선언 이름 앞에 다음 키워드 중 하나를 추가해 만듭니다.
  - public (기본값) : 모든 곳에서 누구나 접근 가능
  - protected : 클래스 내부 또는 하위 클래스에서만 접근 가능
  - private : 클래스 내부에서만 접근 가능
- 타입스크립트의 멤버 접근성은 타입 시스템에서만 존재하는 반면 자바스크립트의 private 선언은 런타임에도 존재한다는 점이 주요 차이점입니다.
- 접근성 제한자 readonly와 명시적 접근성 키워드로 멤버를 선언하려면 접근성 키워드를 먼저 적어야 합니다.
- static 키워드를 사용한 정적 필드의 경우 readonly, public, private, protected와 같이 사용할 수 있습니다. 함께 사용하는 경우 접근성 키워드를 먼저 작성하고, 그 다음 static, readonly 키워드가 옵니다.

## 8.8 마치며
