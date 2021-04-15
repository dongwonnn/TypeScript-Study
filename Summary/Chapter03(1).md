## 객체와 타입

### 3-1. 타입스크립트 변수 선언문

#### 타입스크립트 기본 제공 타입

- 기본 타입 - JS 타입 - TS 타입
  1. 수 타입 - Number - number
  2. 불리언 타입 - Boolean - boolean
  3. 문자열 타입 - String - string
  4. 객체 타입 - Object - object

#### let과 const 키워드

- `let 변수 이름 = [초기 값]` : 수시로 변경 가능.
- `const 변수 이름 = 초기값` : 절대 변하지 않음.

#### 타입 주석

- JS 변수 선언문을 확장해 다음과 같은 형태로 타입을 명시할 수 있습니다. (= 타입주석)
  `let 변수 이름: 타입 [= 초기값]`
  `const 변수 이름: 타입 = 초기값`

- 예)

```ts
let n: number = 1;
let b: boolean = true;
let s: string = "hello";
let o: object = {};
```

- 이 때 let 변수를 변경하려면 타입 주석으로 명시한 타입에 해당하는 값으로만 바꿀 수 있습니다.

```ts
n = "a"; // 타입 불일치
```

#### 타입 추론

- 자바스크립트와 호환성을 위해 타입 주석 부분 생략이 가능합니다.
  = 연산자의 오른쪽 값을 통해 타입을 지정합니다.

```ts
let n = 1; // number
let b = true; // boolean
let s = "hello"; // string
let o = {}; // object
```

#### any 타입

- JS의 호환을 위해 any 타입 제공. 어떤 타입과 무관하다는 뜻.

```ts
let a: any = 0;
a = "hello"; // ok
a = true; // ok
```

#### undefined 타입

- JS에선 변수를 초기화하지 않으면 `undefined` 값을 가집니다.
  하지만 TS에선 타입이기도 하고 값이기도 합니다.

```ts
let u: undefined = undefined;
u = 1; // error
```

- undefined 타입은 TS 타입 중 최하층입니다. 상위 타입 number을 저장하려고 하면 에러가 발생합니다.
- 타입 계층도
  any - number, boolean, string - undefined
  any - object - interface, class.. - undefined

#### 템플릿 문자열

- 변수에 담긴 값으로 문자열을 만들 수 있게 템플릿 문자열 제공합니다.
  `${변수 이름}`

## 3-2. 객체와 인터페이스

- 앞의 object 타입은 객체와 인터페이스의 상위 타입입니다.
  object 타입은 number, boolean, string 타입의 값을 가질 수 없지만, 속성 이름이 다른 객체를 모두 담을 수 있습니다.

```ts
let o: object = { name: "Jack", age: 32 };
```

- 이 타입은 객체를 대상으로 하는 any 타입처럼 동작합니다.
  이것을 해결하기 위해 인터페이스 구문이 고안되었습니다.

#### 인터페이스 선언문

- `interface`는 객체의 타입을 정의할 수 있게 해줍니다.
  객체 타입을 정의하는 것이 목적이므로 {}를 사용하고 속성의 이름과 타입을 나열하는 형태로 사용합니다.

```ts
interface 인터페이스 이름{
    속성 이름: 속성타입
}

interface IPerson{
    name: string,
    age: number
}
```

- 주의점은 인퍼페이스 속성이 모두 있는 객체만 유효하게 동작합니다. 부족하거나 다른 속성이 있으면 오류가 납니다.

```ts
let good: IPerson = { name: "Jack", age: 30 }; // OK
let bad1: IPerson = { name: "Jack" }; // error
let bas2: IPerson = { name: "Jack", age: 30, etc: true }; // error
```

#### 선택 속성 구문

- 어떤 속성은 있어도 되고 없어도 되는 형태 만들 때 사용한다.
  속성 이름 뒤 물음표 기호를 붙여 설정

```ts
interface IPerson {
  number: string;
  age: number;
  etc?: boolean;
}

let good1: IPerson = { name: "Jack", age: 32 }; // OK
```

#### 익명 인터페이스

- interface 키워드를 사용하지 않고 인터페이스의 이름도 없는 인터페이스

```ts
let ai: {
  name: string;
  age: number;
  etc?: boolean;
} = { name: "Jack", age: 32 };
```

- 주로 함수를 구현할 때 사용됩니다.

```ts
function printMe(me: { name: string; age: number; etc?: boolean }) {
  console.log(
    me.etc ? `${me.name} ${me.age} ${me.etc}` : `${me.name} ${me.age}`
  );
}
printMe(ai); // Jack 32
```
