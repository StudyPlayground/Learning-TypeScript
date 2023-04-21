---
marp: true
paginate: true
---

# 제한된 제네릭 타입

---

## 왜 다른 반환 타입이 주어지는가?

```tsx
const roles = {
    favorite: "Fargo",
    others: ["Almost Famous", "Burn After Reading", "Nomadland"],
};

function get1<T, Key extends keyof T>(container: T, key: Key) {
    return container[key];
}

const favorite = get1(roles, "favorite"); // string
const others = get1(roles, "others"); // string[]

// ---cut---

function get2<T>(container: T, key: keyof T) {
    return container[key];
}

const found = get2(roles, "favorite"); // string | string[]
const founds = get2(roles, "others"); // string | string[]
```

---

## 함수의 타입

- function get1<T, Key extends keyof T>(container: T, key: Key): T[Key]
- function get2<T>(container: T, key: keyof T): T[keyof T]

---

## 참고

- 책 10.6 제한된 제네릭 타입
- [Typescript Playground (예시 코드)](https://www.typescriptlang.org/play?#code/GYVwdgxgLglg9mABAcwKZQIwB4AqAaRAaVQE9FUAPKVMAEwGdEBrUuYRHAPgAoIEoAhjDCoATgC4OBFiUnESASkQBvAFCINiUehCikfMIOFiA2jIC6AblUBfVaoP0oWuABtU9DIgC8K9ZuABADc4URhqSQAiADEBUWQ4SLx-DTgoAAsxekkTSIBBVwBbOCdEWOKQeiTESIAhXSQ84GpRRAAlVAFaYWRqyIA5OEKu1wE6SPNkm2sHBFLAkLDqHxR0DG5RNw8MAkiF0PDUSIVLRAB6M8QnMLBkWbBStMzRRl80TA2tz12nrOPTi5XKA3ZAmcz2VSAgC0MIgICgMKhENAkFgCFWUAATLgeAYjCIJFJmKRJDI2BwlGpNFodHpEHihASzKQrLZ7I5nJt3PRMSsqQFggcIjVYvFEslqb8Xjl8kUSs5ynBKn16nSmi12p1urc+oNhrRRuNJmz7vMlXQVu9MZ9uZjdvslkcTudLtceogAD5AkFg03OYDmhiW9DWrkeO01KVVZ2At23T3enpgoA)