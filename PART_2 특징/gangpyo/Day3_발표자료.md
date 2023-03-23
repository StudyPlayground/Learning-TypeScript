# void타입반환의 특이점
- void반환타입을 가지는 함수 타입 별칭은 반환타입을 강제하지 않는다.
    
    → 아무값이나 반환 될 수 있지만, 무시한다.
    
    ```tsx
    type voidFunc = () => void;
     
    const f1: voidFunc = () => { // ok
      return true;
    };
     
    const f2: voidFunc = () => true; // ok
     
    const f3: voidFunc = function () { // ok
      return true;
    };
    
    const f4: voidFunc = () => "문자열";
    
    const v1 = f1(); // v1의 타입은 무시되어 void다
     
    const v2 = f2(); // v2의 타입은 무시되어 void다
     
    const v3 = f3(); // v3의 타입은 무시되어 void다
    
    const v4 = f4().length // error: Property 'length' does not exist on type 'void'.(2339)
    ```
    
- 리터럴 함수 정의가 void반환 값을 가지고 있다면, 그함수는 어떠한 것도 반환해서는 안된다.
    
    ```tsx
    function f1(): void {
      return true; // error: Type 'boolean' is not assignable to type 'void'.(2322)
    }
     
    const f2 = function (): void {
      return true; // error: Type 'boolean' is not assignable to type 'void'.(2322)
    };
    
    const f3 = (): void =>  {
      return true; // error: Type 'boolean' is not assignable to type 'void'.(2322)
    }
    ```
    

# Reference

[Documentation - More on Functions](https://www.typescriptlang.org/ko/docs/handbook/2/functions.html)