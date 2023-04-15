---
marp: true
paginate: true
---

# 타입 서술어 (Type Predicate)

- 타입 서술어는 일반적으로 매개변수로 전달된 인수가 매개변수의 타입보다 더 구체적인 타입인지 여부를 나타내는 데 사용됩니다.

---

## 주석의 변수는 무슨 타입이라고 예상이 되시나요?

```typescript
type ValueType = "0" | "1" | "2";

function test(value: string): value is "2" | "3" {
  return 2 <= Number(value);
}

function ifs(value: ValueType) {
  if (test(value)) {
    return value;
    //      ^?
  }
  return value;
  //       ^?
}
```

---

## 맞추셨나요?

```typescript
type ValueType = "0" | "1" | "2";

function test(value: string): value is "2" | "3" {
  return 2 <= Number(value);
}

function ifs(value: ValueType) {
  if (test(value)) {
    return value;
    //      ^? value: "2"
  }
  return value;
  //       ^? value: "0" | "1"
}
```

---

## 정확한 답변을 찾진 못했어요. 하지만 제 생각에는요!

- 타입 서술어가 있는 함수의 매개변수 타입보다 같거나 구체적인(좁은) 타입을 작성하게 됩니다.
- 그런데 해당 함수가 true일 때 매개변수의 타입은
  - 무조건 타입 서술어로 작성된 타입으로 되는 것이 아니라
  - 인수의 타입과 타입 서술어로 작성된 타입의 intersection으로 결정되는 것 같습니다.
  - `ValueType & "2" | "3"` 의 결과는 `"2"`

---

## 참고

- [예시 코드 (Typescript Playground)](https://www.typescriptlang.org/play?#code/C4TwDgpgBAaghgGwK4QCrmgXigIgAw5QA+uAjISTgEw4DcAUPQGZIB2AxsAJYD2rUwCAGdgUABQA3RCgBcUEQCcurAOYBKOVOTQuQ3DWK4AzIQDe9KJagKIwJAv5UoAHmwA5JAFsARhAWTpCDUGAF9GFg5uPiguJj0A7Tl4bXRINShzKximcUERBJQ1dMysqxs7BygtFAZSqwB6erqAPQB+CyswrPL7fmqIWoamlvaw+kaoQBBxqEAVecAdlsAfTqhAH4nAXEHADJnAGs7ADVXAA5rxpsARUcAM9tm5wBHmqEBVNcAPccAXVcAXnsAdocAXLqhAXBrAFpnADXGoG8AGRcAnIOADUHADzjgB0OqCAFNnAAYdgAwWwApTVBAME1gBFxqCAGVGoIAMIaggAjxwAto4AUsYAdAcYqxBAomHB2NAAPLeABWGXJWX6clYXl8CgYEyyrDgnggckUyhUfKaYwmyipNLpsECrP5Vg5UDwhlIhioktKguFcnwFDIxuodHJ0qasr88ugqAUKFS0HMKssaqMuoFQpFJsMZt1loEGCgrAgAHcnVBsMkUFAAGRQe2OjAMckRTi8fh5URiHjMuSMpkaKB5lm6RMOtDBl3DMq2Xol5kk-pRzDubl+XNN-VBC2MCbpqL8WLxNUxoLK2uWWK5YTAApBYpsurWeuVfqeleuqxtZeWMLbnrrwKbyzbyy7iYhIA)