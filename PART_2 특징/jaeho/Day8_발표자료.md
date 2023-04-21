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
- [Typescript Playground (예시 코드)](https://www.typescriptlang.org/play?#code/MYewdgzgLgBATiANgUwjAvDA3gKBvmAMwEMA3EOASymQC4YAiAMWLgHMQGAaPAkKABbI4EegG0GAQUQBbENBgs5AVwjdGAIWVwwMSYRpwYAJWTEAJpTBt1DAHIgZFxMTDmGAXR4BfANw4cQmUwYChKcBg2ZCgARgAeABUuGABpZABPGGQADxo3NABrDJBCGASAPgAKUDAoYitheiSYIvT6NPSASmxefDho7V0auoa4MVaPf28AmoUScioaDEjomMqEFAhkhnmKamQGTt8YAHoTmGgqaxxZ2H4hEWWo2PWkVG374TUj0-PLqzYYg8ATOMAAtBDgMooBCwQEgiEwhFngAmRJVYb1MCNMrJVr0VolMrdXAEeADHQwTGjcYZSY4aY3cBzEDBcxPaIo16bba7RYHH6g-7WGAAHwuUCugOBtyIrPyHKgXI270YnxEh2OQslALFEqlQKAA)