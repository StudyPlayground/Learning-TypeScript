타입스크립트는 제네릭을 사용해 타입 간의 관계를 알아낸다.

타입스크립트에서 함수와 같은 구조체는 제네릭 타입 매개변수를 원하는 수 만큼 선언할 수 있다.

 제네릭 타입 매개변수는 제네릭 구조체의 각 사용법에 따라 타입이 결정된다. 

이러한 타입 매개변수는 각 인스턴스에서 서로 다른 일부 타입을 나타내기 위해 구조체의 타입으로 사용된다.

타입 매개변수는 전형적으로 T나 U와 같은 단일 문자 이름 또는 Key와 Value 같은 파스칼 케이스 이름을 가진다.

### 제네릭 함수

함수가 여러 개의 타입 매개변수를 선언하면 해당 함수에 대한 호출은 명시적으로 제네릭 타입을 모두 선언하거나 모두 선언해야 한다. 타입스크립트는 아직 제네릭 호출 중 일부 타입만을 유추하지는 못한다.

인터페이스에서도 마찬가지이다. 

제네릭 클래스는 extends 키워드 다음에 오는 기본 클래스로 사용할 수 있다. 타입스크립트는 사용법에서 기본 클래스에 대한 타입 인수를 유추하지 않는다. 기본값이 없는 모든 타입 인수는 명시적으로 타입 애너테이션을 사용해 지정해야 한다. 

```tsx
class Quote<T> {
	lines : T,
	construnctor(lines : T) {
			this.lines = lines;
	}	
}

class SpokenQuote extends Quote<String[]> {
	
}
```

제네릭 파생 클래스는 자체 타입 인수를 기본 클래스에 번갈아 전달할 수 있다. 타입 이름은 일치하지 않아도 된다. 

## 제네릭 타입 별칭

제네릭 판별된 유니언

```tsx
type Result<Data> = FailureResult | SuccessfulResult<Data>

interface FailureResult {
	error : Error;
	succeeded : false;
}

interface SuccessfulResult<data> {
	data : Data;
	succeeded : true;
}

function handleResult(result : Result<string>) {
	if(result.succeeded) {
		console.log('');
	}
}ㄴ
```

ㅈ

제네릭 타입과 판별된 타입을 함께 사용하면 Result와 같은 재사용 가능한 타입을 모델링하는 훌륭한 방법을 제공할 수 있다. 

### 제네릭 기본 값

타입 매개변수 선언 뒤에 =와 기본타입을 배치해 타입 인수를 명시적으로 제공할 수 있다. 기본값은 타입 인수가 명시적으로 선언되지 않고 유추할 수 없는 모든 후속 타입에 사용된다.

```tsx
interface Quote<T = string> {
	value: T;
}
let explicit: Quote<number> = { value : 123 }'
let implicit : Quote = { value : "Be yourself. the Word worships the original" };
let mismatch: Quote = { value: 123 };
```

ㅁ

모든 기본 타입 매개변수는 기본 함수 매개변수 처럼 선언 목록의 제일 마지막에 와야 한다.

기본값이 없는 제네릭 타입은 기본 값이 있는 제네릭 타입 뒤에 오면 안된다.

### 제한된 제네릭 타입

일부 함수는 제한된 타입에서만 작동하는데, extends 키워드를 배치하면 된다.

### keyof과 제한된 타입 매개변수

extends와 keyof를 함께 사용하면 타입 매개변수를 이전 타입 매개변수의 키로 제한할 수 있다. 또한 제네릭 타입의 키를 지정하는 유일한 방법이기도 하다. 

```tsx
function get<T, Key extends keyof T>(container : T, key : Key) {
	return container[key];
}

const roles = {
	favorite : "Fargo",
	others : ["Almost Famous", "Burn After Reading", "Nomadland"]
}

const favorit = get(roles, 'favorite');

```

keyof가 없으면 제네릭 key 매개변수를 올바르게 입력할 방법이 없었을 것이다.

### Promise

임의의 값 타입에 대해 유사한 작업을 나타내는 Promice는 타입스크립트 타입 시스템에서 최종적으로 resolve된 값을 나타내는 단일 타입 매개변수를 가진 Promise 클래스로 표현된다.

### Promise 생성

타입스크립트에서 Promise 생성자는 단일 매개변수를 받도록 작성된다. 해당 매개변수의 타입은 제네릭 Promise 클래스에 선언된 타입 매개변수에 의존한다. 축소된 형식은 다음과 같다. 

```tsx
class PromiseLike<Value> {
	constructor (
		executor: (
			resolve: (value: Value) => void,
			reject : (reson : unknown) => void,
	)
	)

}
```

ㄱ

결과적으로 값을 resolve하려는 Promise를 만드려면 Promise의 타입 인수를 명시적으로 선언해야 한다. 타입스크립트는 명시적 제네릭 타입 인수가 없다면 기본적으로 매개변수 타입을 unknown으로 가정한다. Promise 생성자에 타입 인수를 명시적ㅇ르ㅗ 제공하면 타입스크립트가 결과로서 생기는 Promise 인스턴스의 resolve 타입을 이해할 수 있다. 

```tsx
const resolvesUnknown = new Promise((resolve) => {
	setTimeout(() => resolve('DONE'), 1000)
})

const resolvesUnknown = new Promise<string>((resolve) => {
	setTimeout(() => resolve('DONE'), 1000)
})
```

Promise의 제네릭 .then 메서드는 반환되는 Promise의 resolve된 값을 나타내는 새로운 타입 매개변수를 받는다.

다음 코드는 1초 후에 string 값을 resolve하는 textEventually와 number를 resolve하기 위해 1초를 더 기다리는 legthEventually를 생성한다. 

```tsx
const resolvesUnknown = new Promise((resolve) => {
	setTimeout(() => resolve('DONE'), 1000)
})
const lengthEventually = textEventually.then((text) => text.length)
```

### async함수

자바스크립트에서 async 키워드를 사용해 선언한 모든 함수는 Promise를 반환한다. 자바스크립트에서 async 함수에 따라 반환된 값이 Thenable이 아닌 경우, Promise.resolve가 호출된 것처럼 Promise로 래핑된다.

다음 lengthAfterSecond는 Promse<number>를 직접적으로 반환하는 반면 lengthImmediately는 async 함수이고, 직접 number를 반환하기 때문에 Promise<number>를 반환하는 것으로 간주한다.
