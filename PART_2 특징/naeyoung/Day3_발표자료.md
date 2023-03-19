### void

함수에서 반환 값이 없을 때 반환 타입으로 사용. (undefined 반환)

### never

절대 반환을 하지 않는 함수의 반환 타입 ⇒ 정상적으로 완료되지 않거나(오류 발생), 또는 절대 완료되지 않는 함수

절대 참이 될 수 없는, 일어날 수 없는 변수의 타입. 

```tsx
function a (x : number) : void {
    console.log(x);
}

function b (x : number) : never { //error: A function returning 'never' cannot have a reachable end point.
    console.log(x);

}

const v1 = a(1);
const v2 = b(1);
```

- 무한 루프

```tsx
const sing = function() {
  while (true) {
    console.log("Never gonna give you up");
    console.log("Never gonna let you down");
    console.log("Never gonna run around and desert you");
    console.log("Never gonna make you cry");
    console.log("Never gonna say goodbye");
    console.log("Never gonna tell a lie and hurt you");
  }
};
```

- throw

```tsx
// Type (message: string) => never
const failwith = (message: string) => {
  throw new Error(message);
};
```

함수 표현식과 화살표에서와 달리, 함수 선언식에서의 타입 추론은 다를 수 있다. 이전 버전과의 호환성 때문이다. 따라서 이럴 경우에는 명시적으로 선언해주면 된다. 

```tsx
// Return type: void
function failwith1(message: string) {
  throw new Error(message);
}

// Return type: never
const failwith2 = function(message: string) {
  throw new Error(message);
};

// Return type: never
const failwith3 = (message: string) => {
  throw new Error(message);
}

console.log(failwith1('hi'))
console.log(failwith2('hello'))
```
