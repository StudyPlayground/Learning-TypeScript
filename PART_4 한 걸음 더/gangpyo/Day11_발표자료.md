# 유틸타입 
## 1. Partial`<Type>`

`Type` 집합의 모든 프로퍼티를 선택적으로 타입을 생성한다. 

```ts
interface Todo {
  title: string;
  description: string;
}
 
function updateTodo(todo: Todo, fieldsToUpdate: Partial<Todo>) {
  return { ...todo, ...fieldsToUpdate };
}
 
const todo1 = {
  title: "organize desk",
  description: "clear clutter",
};
 
const todo2 = updateTodo(todo1, {
  description: "throw out trash",
});
```

## 2.Required`<Type>`

`Type`집합의 모든 프로퍼티를 필수로 설정한 타입(Partial의 반대)

```ts
interface Person {
  name: string;
  age?: number;
}
 
const person1: Person = { name: "Jane" }; // ok
const person2: Required<Person> = { name: "sunny" }; // error
// Property 'age' is missing in type '{ name: string; }' but required in type 'Required<Person>'.(2741)

```

## 3. Readonly`<Type>`

`Type` 집합의 모든 프로퍼티*읽기 전용(readonly)*으로 설정한 타입을 생성한다, 즉 생성된 타입의 프로퍼티는 재할당될 수 없다.

```ts
interface Todo {
  title: string;
}
 
const todo: Readonly<Todo> = {
  title: "Delete inactive users",
};
 
todo.title = "Hello"; // error
// Cannot assign to 'title' because it is a read-only property.
```

## 4. Record`<Keys,Type>`

`Type`의 값, `Keys`의 키로구성된 객체를 생성한다. 이 유틸리티는 타입의 프로퍼티를 다른 타입에 매핑 시키는데 사용될 수 있다.

```ts
interface CatInfo {
  age: number;
  breed: string;
}
 
type CatName = "miffy" | "boris" | "mordred";
 
const cats: Record<CatName, CatInfo> = { // ok
  miffy: { age: 10, breed: "Persian" },
  boris: { age: 5, breed: "Maine Coon" },
  mordred: { age: 16, breed: "British Shorthair" },
};

```

## 5. Pick`<Type, Keys>`

`Type`에서 `Keys`(문자열 리터럴이나 문자열 리터럴 유니온)에 해당하는 프로퍼티로 구성된 타입을 만든다.

```ts
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}
 
type TodoPreview = Pick<Todo, "title" | "completed">;
// 아래와 동일.
// {
//	 title: string;
//	 completed: boolean;
//	} 

const todo: TodoPreview = {
  title: "Clean room",
  completed: false,
};
```

## 6. Omit`<Type, Keys>`

Pick와 반대. `Type`에서 `Keys`(문자열 리터럴이나 문자열 리터럴 유니온)를 제외한 프로퍼티로 구성된 타입을 만든다.

```ts
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}
 
type TodoPreview = Omit<Todo, "description" | "completed">;
 
const todo: TodoPreview = { // ok
  title: "Clean room",
};
```

## 7. Exclude`<UnionType,ExcludedMembers>`

`UnionType`에서 `ExcludedMembers`에 할당가능한 유니온 타입을 제외한 유니온타입을 만든다.

```ts
type T0 = Exclude<"a" | "b" | "c", "a">;
// 타입: "b" | "c"

type T1 = Exclude<"a" | "b" | "c", "a" | "b">;
// 타입: "c"

type T2 = Exclude<string | number | (() => void), Function>;
// 타입: string | number;

type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "square"; x: number }
  | { kind: "triangle"; x: number; y: number };
 
type T3 = Exclude<Shape, { kind: "circle" }>
// 타입: { kind: "square"; x: number } | { kind: "triangle"; x: number; y: number };
```

## 8.Extract`<Type, Union>`

`Type`에서 `Union`을 포함하는 타입을 추출하여 새로운 타입을 만든다.

```ts
type T0 = Extract<"a" | "b" | "c", "a" | "f">;
// 타입: "a"
type T1 = Extract<string | number | (() => void), Function>;
// 타입: () => void
 
type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "square"; x: number }
  | { kind: "triangle"; x: number; y: number };
 
type T2 = Extract<Shape, { kind: "circle" }>
// 타입: {
//    kind: "circle";
//    radius: number;
// }
```