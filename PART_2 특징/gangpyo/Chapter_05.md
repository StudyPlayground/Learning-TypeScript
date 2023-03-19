# CHAPTER 5 함수
변수에 타입 애너테이션을 사용해 타입을 선언하듯 함수도 매개변수와 반환타입을 지정해 줄수 있다.

## 5.1 함수 매개변수

- 명시적 타입 정보가 선언되지 않으면 타입스크립트는 이를 any타입으로 간주하여 어떤 타입이든 허용

```jsx
function sing(song) {
  console.log("Singing: ${song}!");
}
```

- string을 사용해 song매개변수가 string 타입임을 타입스크립트에게 알림

```tsx
function sing(song: string) {
  console.log("Singing: ${song}!");
}
```

### 5.1.1 필수 매개변수

- 자바스크립트에서는 인수의 수와 상관 없이 함수를 호출 가능
- 하지만 타입스크립트는 함수에 선언된 모든 매개변수가 필수라고 가정(누락될시 타입에러 발생)

### 5.1.2 선택적 매개변수

```tsx
function sing(song: string) {
  console.log("Singing: ${song}!");
}

function announceSong(song: string, singer?: string) {
  console.log(`Song: ${song}`);

  if(singer) {
    console.log(`Singer: ${singer}`);
  }
}

announceSong("Greensleeves"); // ok
announceSong("Greensleeves",undefined); // ok
announceSong("Greensleeves","Sia"); // ok
```

- 함수에서 사용되는 모든 선택적 매개변수는 마지막 매개변수여야함

```jsx
function announceSong(singer?:string, song : string) {
// error: A required parameter cannot follow an optional parameter.(1016)
// ...
}

```

### 5.1.3 기본 매개변수

- 매개변수에 기본값이 있고 타입 애너테이션이 없는경우, 타입스크립트는 해당 기본값을 기반으로 매개변수타입을 유추
    - 함수내부에서는 기본값의 타입으로 타입이 유추되지만, 호출부에서는 | undefined가 됨

```tsx
function rateSong(song: string, rating = 0) {
  console.log(`Song: ${song} gets ${rating}`);
}

rateSong("Photograph") // ok
rateSong("Set Fire to the Rain",5); // ok
rateSong("Set Fire to the Rain",undefined); // ok: 함수를 호출하는 코드에선 선택적 number | undefined가 된다.

rateSong("At Last!","100"); //error : Argument of type 'string' is not assignable to parameter of type 'number'.(2345)
```

### 5.1.4 나머지 매개변수

- …연산자는 함수선언의 마지막 매개변수에 위치하고, 단일 배열로써 전달되야됨

```tsx
function f1(singer : string , ...songs : string[]) {}

f1("a")
f1("a","b","c")
f1("a","b",1) // error: Argument of type "number" is not assignable to parameter of type "string"
```

## 5.2 반환 타입

함수가 반환할 수 있는 모든 값을 이해하면 반환 타입을 알 수 있음

```tsx
function singSongs (songs: string[]) {
  return songs.length
}
//singSongs함수의 반환타입은 number
```

### 5.2.1 명시적 반환 타입

반환타입을 명시적으로 선언하는 방식이 유용한 상황 3가지

- 가능한 반환값이 많은 경우 항상 동일한 타입의 값을 반환하도록 강제하고 싶은경우
- 타입스크립트는 재귀 함수의 반환 타입을 통해 타입을 유추하는 것을 거부한다 ??
- 수백 개 이상의 타입스크립트 파일이 있는 경우
    - 큰 프로젝트에서 타입스크립트 타입 검사 속도를 높일 수 있음

```tsx

function f1(songs: string[]):number {
  return songs.length
} 

const f2 = (songs: string[]):number => {
  return songs.length
}
```

### 5.3 함수 타입

- 자바스크립트에서는 함수를 값으로 취급할 수 있음으로 이를 받는 타입도 정의 할 수 있어야함
- 함수 타입 구문은 화살표 함수와 유사하지만 함수 본문 대신 타입을 작성

```tsx
let f1 : () => string;
// f1변수의 타입은 매개변수가 없고 반환값이 string인 함수.
```

- 콜백 매개변수를 셜명할때 자주 사용.
- 일치하지 않는 타입의 함수를 호출부에서 전달할때 상세한 메세지를 제공.
    1. 두 함수의 타입 비교출력
    2. 일치하지 않는 부분 비교출력
    3. 일치하지 않는 부분에 대한 할당 가능성 오류 출력

```tsx
const songs = ["song1","song2","song3"];

const runSongs = (getSongAt : (index: number) => string ) => {
songs.forEach((song,index) => {
  console.log(getSongAt(index));
})
}

const getSongAt = (index: number) => {
  return songs[index]
}

const logSong = (song : string) => {
  console.log(song)
}

runSongs(getSongAt);
runSongs(logSong); // error 
// 상세한 오류 메세지 
// 1. Argument of type '(song: string) => string' is not assignable to parameter of type '(index: number) => string'
// 2. Types of parameters 'song' and 'index' are incompatible.
// 3. TYpe 'number' is not assignable to type 'string'.
```

### 5.3.1 함수 타입 괄호

함수 타입도 다른 타입과동일하게 사용되나(유니언 타입도 포함) 괄호에따라 의미가 달라짐

```tsx
// 타입은 string | undefined 유니언을 반환하는 함수
let f1 : () => string | undefined

// 타입은 undefined나 string을 반환하는 함수 (괄호로 구분)
let f2 : (() => string) | undefined
```

### 5.3.2 매개변수 타입 추론

```tsx
let singer: (song: string) => string;
singer = (song) => { // song : string로 추론됨
  return `Singing: ${song.toUpperCase()}`;
}
```

```tsx
const songs = ["song1","song2","song3"];

// 아래와같이 추론됨
// song : string,
// index : number,
songs.forEach((song,index) => {
console.log(`${song} is at index ${index}`);
})
```

### 5.3.3 함수 타입 별칭

3장 ‘유니언과 리터럴’에서 다룬 타입 별칭과 동일하게 함수 타입에서도 타입 별칭을 사용할 수 있음

```tsx
type StringToNumber = (input: string) => number; // 타입별칭 선언

let stringToNumber : StringToNumber;
stringToNumber = (input) => input.length; //ok
stringToNumber = (input) => input.toUpperCase(); // error
```

함수의 매개변수에도 함수 타입을 참조하는 별칭을 사용할 수 있음

```tsx
type NumberToString = (input: number) => string;

function runNumberToString(numberToString : NumberToString) { // 타입 별칭으로 정의
console.log(`The string is : ${numberToString(1234)}`)
}

runNumberToString((input) => `${input}! H`)
```

## 5.4 그 외 반환 타입

void,never타입

### 5.4.1 void 반환 타입

- 함수의 반환 타입을 선언하는데 사용되는 타입스크립트 키워드
- 반환값 없음 (undefined와 다르다)
    - return문 없음
    - 값을 반환하지 않는 return문

```tsx
const f1 = (a : string):void => {} // return문 없음

const f2 = (a : string):void => { // 값을 반환하지 않는 return문
  return  
}

const f3 = (a : string):void => {
	// 반환 타입이 'void'이기때문에 값 반환을 허용하지 않음
  return a.length // error: Type 'number' is not assignable to type 'void'
}
```

- 타입을 선언할때  void를 사용하면 함수에서 반환된 모든 값은 무시된다.

```tsx
let songLogger: (song: string) => void; // 값을 반환하지 않는 함수타입

songLogger = (song) => {
	console.log(`${song}`)
};

songLogger("hello") // ok
```

- 자바스크립트 함수는 실제값이 반환되지 않으면 기본값으로 undefined를 반환하지만 void는 undefined와 동일하지 않음
- void는 함수의 반환 타입이 **무시**된다는것을 의미하고, undefined는 반환되는 리터럴 값임.

```tsx
function f1 () {
  return;  
}

let value: string | undefined;
value = f1(); // error: Type 'void' is not assignable to type 'string' | 'undefined'.
```

- 이렇게 void와 undefined가 구분지어지게되면 void를 반환하는 함수타입에 전달된 함수가 반환한 모든 값을 무시하도록 설정할 수 있음

```tsx
const records : string[] =[];

function saveRecords(newRecords: string[]) {
// 배열 내장함수 forEach메서드는  void를 반환하는 콜백함수를 받음.
// records.push(record)는 number(.push()의 반환값)타입의 값을 반환하지만, forEach메서드의 콜백함수로 허용된다.
newRecords.forEach(record => records.push(record));
}

saveRecords(["11","22","33"]);
```

### 5.4.2 never 반환 타입

- never: 값의 공집합을 의미하는 타입으로, 어떠한 타입도 가질 수 없음을 나타냄(바닥 타입이라고도 불린다)
- 반환값이 없고 반환할 생각조차 없다라는걸 의미하는 타입
    - 의도적으로 오류를 발생하고 싶거나 의도적으로 무한루프를 실행시키고 싶을때 사용
- void는 아무것도 반환하지 않는 함수를 위한 것, never는 절대 반환하지 않는 함수를 위한 것

## 5.5 함수 오버로드

- 매개변수가 다르지만 이름이 동일한 함수를의미
- 오버로드 시그니처, 구현 시그니처로 구분
- 오버로드된 함수 호출에 대해 구문 오류를 생성할지 여부를 결정할때 타입스크립트는 함수의 오버로드 시그니처를 기준으로 확인
- 복잡하고 설명하기 어려운 함수 타입에 사용하는 최후의 수단이기때문에 함수를 단순하게 유지할수 있도록하고 가능하면 사용하지 않는게 좋음

```tsx
// 오버로드 시그니처
function createDate(timestamp: number): Date;

// 오버로드 시그니처
function createDate(month: number, day: number, year:number) : Date;

 // 구현 시그니처
function createDate(monthOrTimestamp: number, day?: number, year?:number) : Date {
return day === undefined || year === undefined 
    ? new Date(monthOrTimestamp) 
    : new Date(year,monthOrTimestamp,day);
}

createDate(554356800)
createDate(7,27,1987)
createDate(4,1) // error: No overload expects 2 arguments, but overloads do exist that expect either 1 or 3 arguments.(2575)

```

### 5.5.1 호출 시그니처 호환성

- 구현시그니처는 모든 오버로드 시그니처와 호환되어야함

```tsx
function format(data: string): string; // ok
function format(data: string,needle:string, haystack:string): string; // ok
function format(getData: () => string): string; // error: This overload signature is not compatible with its implementation signature.(2394)

function format(data: string, needle?:string, haystack?: string) {
    return needle && haystack ? data.replace(needle,haystack) : data;
};
```