## 함수와 메서드

### 4-1. 함수 선언문

- JS 에서 함수는 function 키워드, => 화살표 함수 두 가지가 있습니다.
  TS 에서는 함수 JS 함수 선언문에서 매개변수와 함수 반환값에 타입 주석을 붙이는 형태가 됩니다.

```ts
// 매개변수 1: 매개변수 1 타입, [] .. : 반환값 타입
function add(a: number, b: number): number {
  return a + b;
}
```

#### 매개변수와 반환값으 타입 주석 생략

- 변수와 마찬가지로 함수도 타입 주석을 생략할 수 있지만 지양해야 합니다.
  타입이 생략되어 있으면 함수의 구현 의도를 알기 어렵고 잘못 사용하기 때문입니다.

#### void 타입

- 값을 반환하지 않는 함수는 반환 타입이 void 입니다.
  void 타입은 함수 반환 타입으로만 사용 가능합니다

```ts
function printMe(name: string, age: number) : void {
    ...
}
```

#### 함수 시그니처 (= 함수의 타입)

- 변수에 타입이 있듯, 함수에도 타입이 있는데 이것을 함수 시그니처 라고 합니다.

```ts
// 함수 시그니처 예
// 매개변수가 2개고, 반환값이 void 인 함수의 타입이다.
let printMe: (string, number) => void;

// 적용
let printMe: (string, number) => void = function (
  name: string,
  age: number
): void {};
```

#### tpye 키워드로 타입 별칭 만들기

- `type` 키워드를 통해 기존에 존재하는 타입을 이름만 바꿔서 사용할 수 있게 해줍니다. 이러한 기능을 **타입 별칭** 이라고 합니다.

```ts
// tpye 새로운 타입 = 기존 타입
type stringNumberFunc = (string, number) => void;

let f: stringNumberFunc = function (a: string, b: number): void {};
```

- 만약 매개변수의 개수나 타입, 반환 타입이 다른 함수를 선언하는 잘못을 방지할 수 있습니다.

```ts
let g: stringNumberFunc = function () {}; // Error
```

#### undefined 주의사항

- undefined는 최하위 타입이기 때문에 객체, 인터페이스, 클래스 등을 상속하는 자식 타입으로 간주 됩니다.
  따라서 함수의 인수로 undefined를 넣어도 구문 오류가 발생하지 않습니다.
  하지만 코드를 실행했을 때 오류가 발생하는데 이것을 방지하기 위해 매개변숫값이 undefined인지 판별하는 코드를 작성해야 합니다.

```ts
interface INameable {
    name:string
}

function getName(o : INamealbe){
    retrun o != undefined ? o.name : 'Unknown name'
}

let n = getName(undefined)  // Unknown
let m = getName({name : 'Jack'}) // Jack
```

- 만일 **인터페이스에 선택 속성**이 있는 경우

```ts
interface IAgeable {
  age?: number;
}

function getAge(o: IAgeable) {
  return o != undefined && o.age ? o.age : 0;
}

getAge(undefined); // 0
getAge(null); // 0
getAge({ age: 32 }); // 32
```

#### 선택적 매개 변수

- 인터페이스의 선택 속성 처럼 함수의 매개변수에도 선택적 매개 변수를 설정할 수 있습니다.

```ts
function fn(name : string, age? : number) {
    ...
}
fn('hello', 1)  // hello, 1
fn('hello') // hello, undefined
```

- 함수의 시그니처는 타입 뒤에 물음표를 붙입니다.
  `type OptionalArgFunc = (stirng, number?) => void`

### 4-2. 함수 표현식
