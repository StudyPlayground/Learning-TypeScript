# switch문에서의 never타입 활용
타입스크립트에서 never타입은 어떠한 값도 가질수 없다. 따라서 값을 가질 수 있는 상황이라면 컴파일에러가 발생한다.

```tsx
interface Product {
 color: "red"| "green" | "blue" | "pink";
}

function handleColor(color: Product["color"]): void {
  switch (color) {
    case 'red':
      // ...
      break;
    case 'green':
      // ...
      break;
    case 'blue':
      // ...
      break;
    default:
      const exhaustiveColor: never = color; // error: Type 'string' is not assignable to type 'never'.(2322)
      throw new Error(`Unexpected color: ${exhaustiveColor}`);
  }
}
```

handleColor에서 “switch”문의 모든 경우를 다룰 수 있는지 검사할 수 있다.