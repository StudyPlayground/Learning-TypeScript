다른 타입을 기반으로 한 다른 타입 뿐만 아니라 더 정확한 타입 작성에 중점을 둔 기능을 공부한다.

## 9.1 top 타입

가능한 값이 없고, 접근 불가능한 타입을 설명하기 위해 bottom 타입 개념을 언급했다. 그 반대 개념인 top 타입도 존재한다.

top 타입은 시스템에서 가능한 모든 값을 나타내는 타입이다. 모든 다른 타입의 값은 타입이 top인 위치에 제공될 수 있다. 

즉, 모든 타입은 top 타입에 할당할 수 있다. 

### 9.1.1 any 다시 보기

any 타입은 모든 타입의 위치에 제공될 수 있다는 점에서 top 타입처럼 작도동할 수 있다. any는 일반적으로 console.log에 매개변수와 같이 모든 타입의 데이터를 받아들이는 위치에서 사용한다.

다만 any는 타입스크립트가 해당 값에 대한 할당 가능성 또는 멤버에 대해 타입 검사를 수행하지 않도록 명시적으로 지시한다는 문제점을 가진다. 타입스크립트의 무력화이다. 

따라서 어떤 값이든 될 수 있음을 나타내려면 unkwown 타입이 훨씬 안전하다.

### 9.1.2 unknown

타입스크립트에서 **unknown 타입은 진정한 top 타입**이다. 모든 객체를 unknown 타입의 위치로 전달할 수 있다는 점에서 any 타입과 유사하다. unknown 타입과 any 타입의 주요 차이점은 unknown 타입은 훨씬 제한적이라는 것이다.

- 타입스크립트는 unknown 타입 값의 속성에 직접 접근할 수 없다.
- unknown 타입은 top 타입이 아닌 타입에는 할당할 수 없다.

타입스크립트가 unknown 타입인 name에 접근할 수 있는 유일한 방법은 instanceof나 typeof 또는 type assertion을 사용하는 것 처럼 값의 타입이 제한된 경우다. 

가능하다면 any 보다 unknown을 사용해야 한다.

## 9.2 타입 서술어

instanceof, typeof와 같은 자바스크립트 구문을 사용해 타입을 좁히는 방법을 살펴보았다. 제한된 검사로 이 방법을 직접 사용할 때에는 괜찮지만, 로직을 함수로 감싸면 타입을 좁힐 수 없게 된다.

예시) 함수 안에서 타입 리턴을 해도 타입스크립트는 모른다 

```tsx
function isNumberOrString(value: unknown) {
	return ['number', 'string'].includes(typeof value);
}

function logValueIfExists(value: number | string | null | undefined ){
	if(isNumberOrString(value)) {
		value.toString(); // error
	}
}
```

타입스크립트에는 인수가 특정 타입인지 여부를 나타내기 위해 boolean 값을 반환하는 함수를 위한 구문이 있다. 

이를 타입 서술어라고 부르며 사용자 정의 타입가드라고 부른다. 개발자는 instanceof 또는 typeof와 유사한 자체 타입 가드를 생성한다. 일반적으로 매개변수로 전달된 인수가 매개 변수의 타입 보다 더 구체적인 타입인지 여부를 나타내는데 사용된다.

타입 서술어의 반환 타입은 매개 변수의 이름, is 키워드, 특정 타입으로 선언할 수 있다. 

```tsx
function typePredicate(input: WideType) : input is NarrowType;
```

타입 서술어는 단순 boolean값을 반환하는 것이 아니라 인수가 더 구체적인 타입임을 나타내는 것이다. 타입 서술어는 이미 한 인터페이스의 인스턴스로 알려진 객체가 더 구체적인 인터페이스의 인스턴스인지 여부를 검사하는 데 자주 사용된다. 

타입 서술어는 이미 한 인터페이스의 인스턴스로 알려진 객체가 더 구체적인 인터페이스의 인스턴스인지 여부를 검사하는데 자주 사용된다. 

```tsx
interface Comedian {
	funny : boolean;
}

interface StandupComedian extends Comedian {
	routine : string;
}

function isStandupComedian(value: Comedian): value is StandupComedian {
	return 'routine' in value;
}

function workWithComedian(value: Comedian) {
	if(isStandupComedian(value))
}

cnsole.log(value.routine)
```

타입 서술어는 false 조건에서 타입을 좁히기 때문에 타입 서술어가 입력된 타입 이상을 검색하는 경우 예상치 못한 결과를 얻을 수 있다.

```tsx
function isLongString(input: string | undefined) : input is string{
	return !!(input && input.length >= 7); // input의 length가 7 이상일 때
}

// input의 length가 7 미만인 string도 undefined로 취급
```

대부분은 간단한 타입 서술어만으로도 충분하다.

### 9.3 타입 연산자

키워드나 기존 타입의 이름만 사용해 모든 타입을 나타낼 수는 없다. 때로는 기존 타입의 속성 일부를 변환해서 두 타입을 결합하는 새로운 타입을 생성해야 할 때도 있다. 

### 9.3.1 keyof

자바스크립트 객체는 일반적으로 **string 타입인 동적 값을 사용해 검색된 멤버를 가진다.** 타입 시스템에서 이러한 키를 표현하려면 상당히 까다로울 수 있다. string같은 포괄적인 원시 타입을 사용하면 컨테이너 값에 대해 유효하지 않는 키가 허용된다. 

13장 구성 옵션에서 설명할 더 엄격한 구성 설정을 사용할 때, 타입스크립트는 다음 예제에서 볼 수 있는 것과 같은 스트링 키를 허용하지 않는다. 

```tsx
interface Rates {
	audience : number;
	critics : number;
}

function getRating(ratings: Ratings, key : string) : number { // key : audience | critics
	return rating[key];
	// Error: type string can't be used to index type Ratings
	// No index signature with a parameter of type string was found on type Ratings
}

const ratings : Ratings = { audience : 66, critic : 84 };
get Rating(ratings, 'audience') // 0k
getRating(ratings, 'not valid') // 허용 xxx 하면 안됨

```

또 다른 옵션은 허용되는 키를 위한 유니언 타입을 사용하는 것이다. 그러나 인터페이스에 수십 개 이상의 멤버가 있다면, 각 멤버의 키를 유니언 타입에 모두 입력하고 최신 상태를 유지해야 하는 상당히 번거로운 작업이 필요할 것이다.

대신 타입스크립트에서는 기존에 존재하는 타입을 사용하고, 해당 타입에 허용되는 모든 키의 조합을 반환하는 keyof 연산자를 사용한다. 타입 애너테이션처럼 타입을 사용하는 모든 곳에서 타입 이름 앞에  keyof 연산자를 배치한다. 

다음 keyof  Ratings 는 ‘audience’ | ‘critic’과 동일하지만**, 작성하는 것이 훨씬 빠르고 Ratings 인터페이스가 변경되더라도 수동으로 업데이트할 필요가 없다.** 

```tsx
function getCountKeyof(raings: Ratings, key : keyof Ratings) : number {
	return ratings[key]
}
```

keyof는 존재하는 타입의 키를 바탕으로 유니언 타입을 생성하는 휼륭한 기능이다. 또한 타입스크립트의 다른 타입 연산자와도 잘 결합된다. 

### typeof

타입스크립트에서 제공하는 또 다른 타입 연산자는 typeof이다. typeof는 제공되는 값의 타입을 반환한다. typeof는 값의 타입을 수동으로 작성하는 것이 짜증날 정도로 복잡한 경우에 사용하면 매우 유용하다.

다음 adaption 변수는 original과 동일한 타입으로 선언되었다.

```tsx
const original = {
	medium : "movie",
	title : "Mean Girls",
};

let adaptation : typeof original; // 해당 타입과 동일

if(Math.random() > 0.5) {
	adaptation = {...original, medium : "play"} // Ok
}else {
	adaptation = { ...original, medium : 2 } // error
}
```

typeof 타입 연산자는 시각적으로 주어진 값이 어떤 타입인지를 반환할 때 사용하는 런타임 typeof 연산자처럼 보이지만 이 둘은 차이가 있다. **둘은 단지 우연히 같은 언어를 사용할 뿐이다**. 자바스크립트의 typeof 연산자는 타입에 대한 문자열 이름을 반환하는 런타임 연산이다. 타입스크립트의 typeof 연산자는 타입스크립트에서만 사용할 수 있고 컴파일된 자바스크립트 코드에는 나타나지 않는다**.**

### keyof typeof

**typeof는 값의 타입을 검색하고, keyof는 타입에 허용된 키를 검색한다.** 타입스크립트는 두 키워드를 함께 연결해 값의 타입에 허용된 키를 간결하게 검색할 수 있다. 두 키워드를 함께 사용하면 typeof 타입 연산자를 keyof 타입 연산자와 함께 작업할 때 매우 유용하다.

```tsx
const ratings = {
	imdb: 8.4,
	metacritic : 82
}

function logRating(key : keyof typeof ratings) { //
	console.log(ratings[key])
}

logRatings('imdb') // Ok

```

keyof와 typeof를 결합해 사용하면 명시적 인터페이스 타입이 없는 객체에 허용된 키를 나타내는 타입에 대한 코드를 작성하고 업데이트하는 수고를 줄일 수 있다. 

## 9.4 타입 어서션

타입스크립트는 코드가 강력하게 타입화 될 때 가장 잘 작동한다. 즉, 코드의 모든 값이 정확히 알려진 타입을 가지는 경우이다. **타입스크립트의 타입 검사기가 복잡한 코드를 이해할 수 있도록 top 타입과 타입 가드 같은 기능을 제공한다**. 그러나 경우에 따라서는 코드가 어떻게 작동하는지 타입 시스템에 100% 정확하게 알리는 것이 불가능할 수 있다. 

예를 들어 JSON.parse는 의도적으로 top 타입인 any를 반환한다. JSON.parse에 주어진 특정 문자열 값이 특정한 값 타입을 반환해야 한다는 것을 타입 시스템에 안전하게 알릴 수 있는 방법이 없다. 

반환 타입에 대해 한 번만 사용되는 제네릭 타입을 추가하는 것은 제네릭의 황금률로 알려진 모범사례를 위반한다. 

타입스크립트는 값의 타입에 대한 타입 시스템의 이해를 제정의하기 위해 타입 어셔션을 제공한다. as 키워드로. 

타입스크립트 모범 사례는 가능한 한 타입 어설션을 사용하지 않는 것이다. 코드가 완전히 타입화되고 어서션을 사용해 타입스크립트의 타입 이해를 방해할 필요가 없는 것이 가장 좋다.

### 9.4.1 포착된 오류 타입 어서션

오류를 처리할 때 타입 어설션이 매우 유용할 수 있다. try 블록의 코드가 예상과 다른 객체를 예기치 않게 발생할 수 있기 때문에 catch 블록에서 포착된 오류가 어떤 타입인지 아는 것은 일반적으로 불가능하다. 게다가 자바스크립트의 모범 사례는 항상 Error 클래스를 throw하지만 일부 프로젝트에서는 문자열 리터럴 또는 다른 의외의 값을 발생시키기도 한다.

코드 영역이 Error 클래스의 인스턴스를 발생시킬 것이라고 틀림 없이 확신한다면 타입스크립트를 사용해 포착된 어서션을 오류로 처리할 수 있다. 

하지만 발생된 오류가 예상된 오류 타입인지를 확인하기 위해 instanceof 검사와 같은 타입 내로잉을 사용하는 것이 더 안전하다. 

### 9.4.2 non-null 어서션

타입 어서션이 유용한 경우를 더 살펴 보자면, 실제로는 아니고 이론적으로만 null 또는 undefined를 포함할 수 있는 변수에서 null과 undefined를 제거할 때 타입 assertion을 주로 사용한다.  타입스크립트에서는 너무 흔한 상황이라 이와 관련된 악어를 제공한다. ! 이다. 

not-null assertion은 타입이 null 또는 undefined가 아니라고 간주한다.

```tsx
let maybeDate : Date | undefined

maybeDate! // Date
maybeDate as Date // Date
```

non-null assertion은 값을 반환하거나 존재하지 않는 경우 undefined를 반환하는 Map.get과 같은 API에서 특히 유용하다. 

### 9.4.3 타입 어서션 주의 사항

any 타입과 마찬가지로 타입 어서션은 타입스크립트의 타입 시스템에 필요한 하나의 도피 수단이다. 따라서 any 타입을 사용할 때 처럼 꼭 필요한 경우가 아니라면 가능한 한 사용하지 말아야한다. 값의 타입에 대해 더 쉽게 assertion 하는 것 보다 코드를 나타내는 더 정확한 타입을 갖는 것이 좋다. 또한 이러한 assertion은 잘못될 가능성이 있다. **따라서 사용하는 것이 안전하다고 확실히 확신할 때만 사용해야 한다.** 

어서션 vs 선언

변수 타입을 선언하기 위해 타입 애너테이션을 사용하는 것과 초깃값으로 변수 타입을 변경하기 위해 타입 어서션을 사용하는 것 사이에는 차이가 있다. 

변수의 타입 애너테이션과 초깃값이 모두 있을 때, 타입스크립트의 타입 검사기는 변수의 타입 애너테이션에 대한 변수의 초깃값에 대해 할당 가능성 검사를 수행한다. 

그러나 타입 어서션은 타입 스크립트에 타입 검사 중 일부를 건너 뛰도록 명시적으로 지시한다. 

### 어서션 할당 가능성

타입 어서션은 일부 값의 타입이 약간 잘못된 상황에서 필요한 작은 도피 수단이다. 타입 스크립트는 타입 중 하나가 다른 타입에 할당 가능한 경우에만 두 타입 간의 ㅏ입 어서션을 허용한다. 

하나의 타입에서 값을 완전히 관련 없는 타입으로 전환해야 하는 경우 이중 타입 어서션을 사용해야 한다. 값을 any나 unknown 같은 탑 타입으로 전환 후 그 결과를 관련 없는 타입으로 전환한다.

**as unknown as.. 이중 타입 어서션은 위험하고 거의 항상 타입이 잘못되었다는 징후이다.**

### 9.5 const assertion

const assertion은 배열, 원시타입, 값, 별칭 등 모든 값을 상수로 취급해야 함을 나타내는데 사용한다. 특히 as const 는 수신하는 모든 타입에 세 가지 규칙을 적용한다. 

- 배열은 가변 배열이 아닌 읽기 전용 튜플로 취급된다.
- 리터럴은 일반적인 원시 타입과 동등하지 않고 리터럴로 취급된다.
- 객체의 속성은 읽기 전용으로 간주된다.

### 9.5.1 리터럴에서 원시 타입으로

타입시스템이 리터럴 값을 일반적인 원시 타입으로 확장하기보다 틀정 리터럴로 이해하는 것이 유용할 수 있다.

튜플을 반환하는 함수처럼 일반적인 원시 타입 대신 특정 리터럴을 생성한다고 알려진 함수에서 유용할 수 있다. 다음 함수는 좀 더 구체적으로 만들 수 있는 값을 반환한다. 여기에서 getNameConst의 반환 타입은 일반적인 string 대신 구체적인 값이다.

```tsx
const getName = () => "Maria Bamford";
const getNameConst = () => "Maria Bamford" as const
```

값의 특정 필드가 더 구체적인 리터럴 값을 갖게 하는 것도 유용하다. 많은 인기 있는 라이브러리는 값의 판별 필드가 특정 리터럴이 되도록 요구한다. 따라서 해당 코드의 타입이 값을 더 구체적으로 추론할 수 있다.

### 9.5.2 읽기 전용 객체

변수의 초깃값으로 사용되는 것과 같은 객체 리터럴은 let 변수의 초깃값이 확장되는 것과 동일한 방식으로 속성 타입을 확장한다. apple같은 문자열 값은 string과 같은 원시 타입이 되고, 배열은 튜플이 아닌 array타입이된다.

그러나 as const를 사용하면 유추된 타입이 가능한 한 구체적으로 전환된다. 모든 멤버 속성은 readonly가 되고, 리터럴은 일반적인 원시 타입 대신 고유한 리터럴 타입으로 간주되며, 배열은 읽기 전용 튜플이 된다. 즉, 값 리터럴에 const assertion을 적용하면 해당 값 리터럴이 변경되지 않고 모든 멤버에 동일한 const assertion 로직이 재귀적으로 적용된다.
