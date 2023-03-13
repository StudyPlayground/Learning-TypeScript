# 덕 타이핑 vs 구조적 타이핑
## 1. 덕타이핑

- 동적 타이핑중 하나로, 객체의 변수 및 메소드의 집합이 객체의 타입을 결정하는 것을 의미
- 타입검사를 런타임에 진행

```jsx
class Duck {
  quack() {
    console.log('꽥!')
  }
  feathers() {
    console.log('깃털은 검정색과 흰색')
  }
}

class Human {
  quack() {
    console.log('이 사람이 오리를 흉내내네요.')
  }
  feathers() {
    console.log('사람이라 깃털은 없어요. 하지만 털은 있습니다.')
  }
}

function inTheForest(duck) { // quack메서드와 feathers메서드가 존재하면 같은 타입으로 간주
  duck.quack();
  duck.feathers();
}

inTheForest(new Duck()); 
inTheForest(new Human());
```

- 장점: 타입검사를 런타임에 진행하기 떄문에 빠르게 개발가능
- 단점: 타입검사를 런타임에 진행하기 때문에 개발과정에서 에러를 확인하기가 어려움

## 2. 구조적 타이핑( 구조적 타입 시스템)

- 구조적으로 동일하면 호환가능한 타입이 될 수 있음을 의미
- 명목적 타이핑(C,java 등등)과 같은 명확한 상속관계(A - B)를 지향하기보다 집합으로 포함된다는 개념을 지향
- 런타임이아닌 컴파일단계에서 타입을 체크

```jsx
type WithFirstName = {
    firstName: string;
}

type WithLastName = {
    lastName : string;
}

const hasBoth = {
    firstName : "Lucille",
    lastName : "Clifton"
}

*let withFirstName : WithFirstName* = hasBoth; // OK: hasBoth는 string 타입인 firstName을 포함함
let withLastName: WithLastName = hasBoth; // OK: hasBoth는 string 타입인 lastName을 포함함
```

## 레퍼런스

[덕 타이핑](https://ko.wikipedia.org/wiki/덕_타이핑)

[덕 타이핑과 구조적 타이핑](https://vallista.kr/덕-타이핑과-구조적-타이핑/)

[명목적 자료형 체계](https://ko.wikipedia.org/wiki/명목적_자료형_체계)