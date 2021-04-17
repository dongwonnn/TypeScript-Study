## 함수와 메서드

### 4-1. 함수 선언문

- JS 에서 함수는 function 키워드, => 화살표 함수 두 가지가 있습니다.
  TS 에서는 JS 함수 선언문에서 **매개변수**와 **함수 반환값**에 **타입 주석**을 붙이는 형태가 됩니다.

```ts
// 매개변수 1: 매개변수 1 타입, [] .. : 반환값 타입
function add(a: number, b: number): number {
  return a + b;
}
```

#### 매개변수와 반환값으 타입 주석 생략

- 변수와 마찬가지로 함수도 타입 주석을 생략할 수 있지만 **지양**해야 합니다.
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

- 변수에 타입이 있듯, **함수에도 타입**이 있는데 이것을 **함수 시그니처** 라고 합니다.

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

- **`type`** 키워드를 통해 기존에 존재하는 타입을 이름만 바꿔서 사용할 수 있게 해줍니다. 이러한 기능을 **타입 별칭** 이라고 합니다.

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

- 인터페이스의 선택 속성 처럼 함수의 매개변수에도 **선택적 매개 변수**를 설정할 수 있습니다.

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

#### 함수는 객체다.

- JS는 **객체지향 언어와 함수형 언어의 특징**이 모두 있습니다.
- JS는 **Function 클래스의 인스턴스** 입니다.

```ts
let add = new Fucntion("a", "b", "retrun a+b");
let result = add(1, 2); // 3
```

- 함수를 구현하는 방법
  1. 일반적인 방법
     `function add(a,b){return a+b}`
  2. 함수 이름을 제외한 방법 (**=함수 표현식**)
     `let add = function(a,b) {return a+b}`
     위 코드 처럼 함수 이름을 제외한 코드를 함수 표현식이라고 합니다.
     함수 표현식은 함수형 언어의 핵심 기능입니다.

#### 일등 함수

- 프로그래밍 언어가 일등함수 기능을 제공하면 함수형 프로그래밍 언어라고 합니다.
- **일등함수**

  > **함수와 변수를 구분하지 않는다.**

- 다음 코드에서 `f`는 `let` 키워드를 사용한 변수임과 동시에 함수 표현식을 저장했습니다.
  이때 `f`를 변수인지 함수인지 사실상 구분할 수 없고 이것을 **변수와 함수를 차별하지 않는다**는 의미입니다.

```ts
let f = function (a, b) {
  return a + b;
};
f = function (a, b) {
  return a - b;
};
```

#### 표현식과 계산법

- **표현식**은 `리터럴`,`연산자`,`변수`,`함수 호출` 등이 복합적으로 구성된 코드 형태를 말합니다. 이것은 컴파일러의 **계산법**이 적용되어 어떤 값이 됩니다.
- 컴파일러는 표현식을 만나면 계산법을 적용해 어떤 값을 만듭니다.
  1. 조급한 계산법
     **함수의 몸통 부분**을 실행합니다.
  2. 느긋한 계산법
     함수 표현식 `function(a,b) {return a+b}`를 만나면 a,b가 어떤 값인지 알 수 없어서 느긋한 계산법을 적용해 계산을 보류합니다.

#### 함수 호출 연산자

- 어떤 변수가 함수 표현식을 가지고 있다면 **함수 호출 연산자** `()`를 이용해 호출할 수 있습니다.
  여기서 함수 호출이란 함수 표현식이 **몸통 부분**을 실행한다는 뜻입니다. 매개변수가 있다면 `()`안에 매개변수를 명시할 수 있습니다.

```ts
let add = function (a, b) {
  return a + b;
};
let value = add(1, 2); // 3
```

- 컴파일러는 함수 호출문을 만나면 **느긋한 계산법에서 조급한 계산법**을 적용해 함수 표현식을 식으로 적용합니다.
  즉 value에 add(1,2)를 통해 호출되면, add에 저장된 함수 표현식을 끄집어 내 조급한 계산법을 적용합니다.
  즉, 함수 표현식의 몸통 부분인 return a+b 에서 **a,b의 값이 확정되면** 몸통은 return 1+2, 조급한 계산법이 적용되면 return 3이 됩니다.

#### 익명함수

- 함수 표현식은 익명 함수의 다른 표현입니다.
- **익명함수**의 동작 원리
  1. `function(a,b) { return a+b }`를 통해 게으른 계산법을 적용 시킨 후,
  2. (1,2)를 통해 조급한 계산법으로 적용, 3을 얻습니다.
  3. value 변수에 값 대입.

```ts
let value = (function (a, b) {
  return a + b;
})(1, 2);
```

#### const 키워드와 함수 표현식

- 함수 표현식은 `let`키워드로 선언하면 내용이 바뀔 수 있어 **`const`** 키워드로 선언하는 것이 바람직합니다.
