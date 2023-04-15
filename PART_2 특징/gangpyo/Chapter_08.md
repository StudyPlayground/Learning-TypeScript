# CHAPTER 8 클래스

## 8.1 클래스 메서드

다음은 string 타입의 단일 필수 매개변수를 갖는 greet클래스 메서드를 가진 Greeter클래스를 의미한다.

```tsx
class Greeter {
    greet(name:string) {
        console.log(`${name}, do your stuff!`);
    }
}

new Greeter().greet("Miss Frizzle");
new Greeter().greet(); // Expected 1 arguments, but got 0.(2554)
```

## 8.2 클래스 속성

타입스크립트에서 클래스의 속성을 읽거나 쓰려면 클래스에 명시적으로 선언해야한다.

클래스 속성은 인터페이스와 동일한 구문을 사용해 선언한다.

클래스 속성 이름 뒤에는 선택적으로 타입 애너테이션이 붙을 수 있다.

클래스 속성을 명시적으로 선언하면 타입스크립트는 클래스 인스턴스에 허용되는 속성을 빠르게 이해할 수 있다.

### 8.2.1 함수 속성

자바스크립트에는 클래스의 멤버로 호출이 가능한 함수로 선언하는 두가지 방법이 있다.

1. 메서드 접근

메서드 접근은 함수를 클래스 프로토타입에 할당하므로 모든 인스턴스는 동일한 함수 정의를 사용하게된다.

```jsx
class WithMethod {
    myMethod() {}
}
new WithMethod().myMethod() === new WithMethod().myMethod; // true
```

1. 값이 함수인 속성을 선언하는 방식

속성으로 선언된 함수는 인스턴스마다 새로운 함수를 선언하게된다.

```jsx
class WithProperty {
    myProperty: () => {}
}
new WithProperty.myProperty === new WithProperty.myProperty; // false
```

### 8.2.2 초기화 검사

타입스크립트 컴파일 옵션중 strictPropertyInitialization옵션을 적용하면 클래스에 선언된 속성이 초기화 과정에서 할당하지 않을경우 에러를 발생한다.

```tsx
class WithValue {
	    immediate = 0;
	    later:number;
	    mayBeUndefined: number | undefined; // ok (undefined가 되는것을 허용)
	    unused:number; // error : 초기화가 안되었기 때문
	
	    constructor() {
	        this.later = 1;
	    }
    }
```

### 8.2.3 선택적 속성

인터페이스와 마찬가지로 선언된 속성 이름 뒤에? 를 추가해 속성을 옵션으로 선언할 수 있다. | undefined를 포함하는 유니언타입과 거의 동일하게 작동한다.

```tsx
class MissingInitializer {
    property?: string; // 선택적 속성으로 선언되었음으로 error가 발생하지 않는다.
}
```

### 8.2.4 읽기 전용 속성

인터페이스와 마찬가지로 선언된속성이름 앞에 readonly키워드를 추가해 속성을 읽기전용으로 선언할 수 있다.

readonly키워드는 타입스크립트에서만 존재하며 자바스크립트로 컴파일될때 사라진다.

readonly로 선언된 속성은 선언된 위치 or 생성자에서 초깃값만 설정할 수 있다.

```tsx
class Person {
    readonly birth: number;
    
    constructor() {
        this.birth = 19960229;
    }

    updateInfo () {
        this.birth = 20000101; // error: Cannot assign to 'birth' because it is a read-only property.(2540)
    } 
}
```

→ 단, npm패키지로 코드를 우리가 게시하여 다른사람이 가져다 사용하는상황을 가정해봤을때, readonly제한자가 적용되지 않는 환경일 수도 있다. 특히 사용하는 사람이 자바스크립트로 개발중인 상황이라면 말이다. 그래서 진정한 읽기전용으로 보호받길 원한다면 #private필드나 get()함수 속성을 사용할 것을 고려해보자.

원시타입의 초기값을 갖는 readonly로 선언된 속성은 다른 속성과는 좀 다르게 동작한다. 

string으로 확장하기 위해선 타입 애너테이션을 추가해야한다.

```tsx
class Person {
    readonly address = "anyang";
    readonly phone:string  = "01011111111";

    constructor() {
        this.address = "gangnam"; // error
        this.phone = "01022222222"; // ok
    }
}
```

## 8.3 타입으로서의 클래스

타입 시스템에서의 클래스는 런타임값(클래스 자체)과 타입 애너테이션에서 사용할 수 있는 타입을 생성한다.

```tsx
class Person {
    sayHi() {
        console.log("hi")
    }
}

let teacher: Person; // 타입으로도 사용됨

teacher = new Person();

teacher  = "haha"; // error : Type 'string' is not assignable to type 'Person'.(2322)
```

**타입스크립트는 클래스의 동일한 멤버를 모두 포함하면, 해당 클래스에 할당할 수 있는것으로 간주한다(타입스크립트 구조적타이핑은 선언되는 방식이아닌 객체의 형태만 고려하기때문)**

```tsx
class Person {
    sayHi() {
        console.log("hi")
    }
}

function withPerson (p1: Person) {
p1.sayHi();
}

withPerson(new Person()); // ok
withPerson({sayHi() {console.log("hello")}}); // ok
```

## 8.4 클래스와 인터페이스

인터페이스는 연관된 이름으로 객체의 형태를 설명하는 방법이다.

마찬가지로 타입스크립트는 클래스 이름 뒤에 implements키워드와 인터페이스 이름을 추가함으로써 클래스의 인스턴스가 인터페이스를 준수한다고 선언할 수 있다.

타입 검사기에의해 모든 불일치에대해서 타입 오류가 발생한다.

```tsx
interface Person {
    name: string;
    sayHi(p1: string): void;
}

class Teacher implements Person {
    name: string;
    constructor() {
        this.name = "brown"
    }

    sayHi(p1:string) {
        console.log(p1);
    }
}

class Student implements Person { 
// error
// Class 'Student' incorrectly implements interface 'Person'.
// Property 'sayHi' is missing in type 'Student' but required in type 'Person'
    name = "Rocky"
}
```

인터페이스에서 클래스의 메서드 또는 속성타입을 유추하지 않는다.

- 타입 애너테이션을 지정하지 않는한 any로 간주한다.

```tsx
interface Person {
    name: string;
    sayHi(p1: string): void;
}

class Teacher implements Person {
    name;// error: Member 'name' implicitly has an 'any' type.(7008)
    sayHi(p1) { // error: Parameter 'p1' implicitly has an 'any' type.(7006)
        console.log(p1);
    }
}
```

### 8.4.1 다중 인터페이스 구현

인터페이스 이름 사이에 쉼표를 넣고, 개수 제한 없이 사용가능

```tsx
interface Graded {
    grades:number[];
}

interface Reporter {
    report:() => string;
}

class ReportCard implements Graded, Reporter {
	grades: number[];
	
	constructor () {
	    this.grades = [1,2,3];
	}
	
	report() {
	    return this.grades.join(",");
	}
	
}

class Empty implements Graded, Reporter {} 
// error
// Class 'Empty' incorrectly implements interface 'Graded'.
// Property 'grades' is missing in type 'Empty' but required in type 'Graded'.(2420)
// input.tsx(2, 5): 'grades' is declared here.

// Class 'Empty' incorrectly implements interface 'Reporter'.
// Property 'report' is missing in type 'Empty' but required in type 'Reporter'.(2420)
// input.tsx(6, 5): 'report' is declared here.
```

## 8.5 클래스 확장

기본클래스에서 선언된 모든 메서드나 속성은 하위 클래스에서 사용할 수 있다.

```tsx
class Teacher {
    teach() {
        console.log("teach something");
    }
}

class StudentTeacher extends Teacher {
    learn() {
        console.log("learn for teaching")
    }
}

const teacher = new StudentTeacher(); // 파생클래스에의해 인스턴스 생성

teacher.teach(); // 기본클래스에서 선언함
teacher.learn(); // 파생클래스에서 선언함
```

### 8.5.1 할당 가능성 확장

하위 클래스의 인스턴스는 기본 클래스의 모든 멤버를 가지고 있으므로 기본 클래스의 인스턴스가 필요한 곳에서 사용할 수 있다.

```tsx
class Person {
    name :string;
    constructor(name:string) {
        this.name = name;
    }
}

class Teacher extends Person {
    subject: string;

    constructor(name:string,subject:string) {
        super(name);
        this.subject = subject;
    }
}

let person: Person;
person = new Person("king"); // ok
person = new Teacher("king","cs"); // ok: 기본클래스의 멤버를 모두 가지고 있기때문
```

### 8.5.2 재정의된 생성자

자바스크립트와 마찬가지로 하위 클래스는 자체 생성자를 정의할 필요가 없다. 

자체 생성자를 작성하지 않으면 암묵적으로 기본 클래스의 생성자를 사용한다.

```tsx
class Person {
    name :string;
    constructor(name : string) {
        this.name = name;
    }
}

class Teacher extends Person {
    teach() {
        console.log("teach something");
    }
}

const person: Teacher = new Teacher("kate");
person.teach(); // ok
console.log(person.name); // ok
```

자바스크립트와 마찬가지로 하위클래스가 자체 생성자를 선언하게되면 super키워드를 통해 기본 클래스의 생성자를 호출해야한다.

타입스크립트는 super()를 호출하기전에 this 나 super에 접근하려하면 타입 오류를 발생시킨다.

```tsx
class Person {
    name :string;
    constructor(name : string) {
        this.name = name;
    }
}

class Teacher extends Person {
    subject :string;

    constructor(name:string, subject:string) {
        this.subject = subject; // error
        super(name);
    }

    teach() {
        console.log("teach something");
    }
}
```

### 8.5.3 재정의된 메서드

하위 클래스의 메서드가 기본 클래스의 메서드에 할당될 수 있는 한 하위 클래스에서 동일한 이름으로 새로운 메서드를 선언할 수 있다. 단, 타입도 기본 메서드 대신 사용할 수 있어야한다.(구조적으로 동일해야함)

### 8.5.4 재정의된 속성

재정의된 메서드와 마찬가지로 동일한 이름으로 기본 클래스의 속성을 명시적으로 다시 선언할 수 있고, 타입도 대신 사용할 수 있어야한다.(구조적으로 동일해야함)

- 유니언타입 구체화

```tsx
class Assignment {
    grade?: number;    
}

class GradedAssignment extends Assignment {
    grade: number;

    constructor(grade: number) {
        super();
        this.grade = grade;
    }
}
```

- 속성의 유니언 타입의 허용된 값 집합을 확장할 수는 없다.

```tsx
class Assignment {
    grade = 100; 
}

class GradedAssignment extends Assignment {
    grade = Math.random() > 0.5 ? 10 : "..."; 
// error 
// Property 'grade' in type 'GradedAssignment' is not assignable to the same property in base type 'Assignment'.
// Type 'string | number' is not assignable to type 'number'.
// Type 'string' is not assignable to type 'number'.
}
```

## 8.6 추상 클래스

일부 메서드의 구현을 선언하지 않고, 하위 클래스가 제공할 것을 예상하고 기본클래스를 만들수 있다. 추상화하려 는 클래스앞과 메서드앞에 타입스크립트의 abstract 키워드를 추가해준다. 추상화 기본 클래스에선 메서드의 본문을 제공하는것을 건너뛰고 인터페이스에서 선언한것처럼 선언한다.

```tsx
abstract class School {
    readonly name : string;

    constructor(name : string) {
        this.name = name;
    }
    
    abstract getStudentTypes(): string[]; // interface에서 선언하는방식처럼 선언
}

class Preschool extends School {
    getStudentTypes() { // ok
        return ["perschooler"];
    }
}

class Absence extends School {}
//error
// Non-abstract class 'Absence' does not implement inherited abstract member 'getStudentTypes' from class 'School'.
```

추상클래스를 호출하면 에러가 발생한다.

```tsx
abstract class School {
    readonly name : string;

    constructor(name : string) {
        this.name = name;
    }
    
    abstract getStudentTypes(): string[];
}

class Preschool extends School {
    getStudentTypes() {
        return ["perschooler"];
    }
}

const preSchool = new Preschool("Sunnyside Daycare");
const school = new School("somewhere else"); // error 
```

추상 클래스는 타입애너테이션으로써는 사용될 수 있지만, 인스턴스를 생성하기위해선 하위 클래스를 사용해야한다.

## 8.7 멤버 접근성

자바스크립트에서는 클래스 멤버 앞에 #을 추가해 private클래스 멤버임을 나타낸다.

private클래스 멤버는 해당 클래스 내부에서만 접근가능하다.

런타임에서 private메서드나 속성에 접근하려하면 오류를 발생시킨다.

```jsx
class Person {
	  #name
    constructor(name){
        this.#name = name
    }

    printName() {
        console.log(this.#name)
    }
}

const jane = new Person("jane");
jane.#name // error
// Property '#name' is not accessible outside class 'Person' 
// because it has a private identifier.
```

타입스크립트의 멤버 접근성은 클래스 멥버의 선언 이름 앞에 다음 접근성키워드중 하나를 추가해 만든다.

1. public(기본값): 모든곳에서 누구나 접근가능
2. protected: 클래스 내부 또는 하위 클래스에서만 접근 가능
3. private: 클래스 내부에서만 접근 가능

**3가지 키워드는 타입 시스템내에서만 존재함으로 코드가 자바스크립트로 컴파일되면 다른 타입시스템 구문과 함께 제거된다.**

**→ 자바스크립트 런타임에서는 #으로 작성된 private필드만 진정한 private이다.**

readonly키워드와 함께 사용할 수도 있다.

```tsx
class Person {
    private readonly name:string;

    constructor() {
        this.name = "Anna";
    }

    print () {
        console.log(this.name);
    }

}

const Anna = new Person();
Anna.print();

Anna.name = "elsa"; // error
// Property 'name' is private and only accessible within class 'Person'.(2341)
// Cannot assign to 'name' because it is a read-only property.(2540)
// (property) Person.name: any
```

### 8.7.1 정적 필드 제한자

자바스크립트는 static 키워드를 사용해 클래스 자체에서 멤버를 선언한다.

타입스크립트는 static 키워드를 단독으로 사용하거나 readonly와 접근성 키워드를 함께 사용할 수 있도록 지원한다.

```tsx
class Question {
    protected static readonly answer = "bash";
    protected static readonly prompt = "what`s an ogre's favorite programming language?";
    
    guess(getAnswer: (prompt:string) => string) {        
        const answer = getAnswer(Question.prompt);
        if(answer === Question.answer) {
            console.log("you got it!");
        } else {
            console.log("Try again...");
        }
    }
}

Question.answer; // error: 
// Property 'answer' is protected and only accessible within class
// 'Question' and its subclasses.
```