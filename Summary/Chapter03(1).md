## 객체와 타입

### 1-1. 타입스크립트 변수 선언문

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

### 3-3. 객체와 클래스

#### 클래스 선언문

- `class`, `private`, `public`, `protected`, implements`, `extends 키워드 제공.
- new 연산자를 이용해 jack1 이라는 이름의 Person1 타입 변수 생성

```ts
class Person1{
    name: stirng,
    age? :number
}

let jack1 : Person1 = new Person1()
jack1.name = 'Jack'
jack1.age = 32;
```

#### 접근 제한자

- `public`, `private`, `protected` 와 같은 접근 제한자 제공.
  default로 `public`

#### 생성자

- 클래스는 constructor(생성자)라는 특별한 메서드를 포함합니다.
- 타입스크립트 클래스는 다음과 같은 형태로 클래스의 속성을 선언할 수 있습니다.

```ts
class Person2 {
  constructor(public name: string, public age?: number);
}
let jack2: Person2 = new Person2("Jack", 32);
```

- public 제한 접근자를 붙이면 매개변수의 이름을 가진 속성이 클래스에 선언된 것처럼 동작합니다.
  Person2 는 Person3 을 함축해서 구현한 것입니다.

```ts
class Person3 {
    name : string,
    age? : number,
    constructor(name: string, age? : number){
        this.name = name;
        this.age = age;
    }
}

let jack3 : Person3 = new Person3('Jack', 32)
```

#### 클래스에서 인터페이스 구현

- `implements` 키워드를 사용하여 인터페이스를 구현합니다.

```ts
// Person 4 방법
interface IPerson4{
    name: string,
    age? : number
}

class Person4 implements IPerson4{
    name: string,
    age: number
}
```

```ts
// Person 2 방법
interface IPerson4 {
  name: string;
  age?: number;
}

class Person4 implements IPerson4 {
  constructor(public name: string, public age?: number) {}
}
let jact4: IPerson4 = new Person4("Jack", 32);
```

#### 추상 클래스

- `abstract` 키워드를 사용해 추상 클래스를 만들 수 있습니다.
  추상 클래스는 자신의 속성이나 메서드 앞에 `abstract`를 붙여 나를 상속하는 다른 클래스에서 이 속성이나 메서드를 구현하게 합니다.

- `abstract`가 붙었으므로 new 연산자를 적용해 객체를 만들 수 없습니다.

```ts
abstract class AbstractPerson5{
    abstract name : string,
    constructor (public age? : number) {}
}
```

#### 클래스와 상속

- `extents` 키워드를 이용해 부모 클래스를 상속받는 상속 클래스를 만들 수 있습니다.

```ts
class Person5 extends AbstractPerson5 {
  constructor(public name: string, public age?: number) {
    super(age);
  }
}
let jack5: Person5 = new Person5("Jack", 32);
```

#### static 속성

- 정적인 속성 가질 수 있습니다

```ts
class A {
  static initValue = 1;
}
let initVal = A.initValue;
```

### 3-4. 객체의 비구조화 할당문

- 관련된 정보를 묶어 새로운 타입으로 표현하는 것을 구조화라고 합니다.
- 구조화를 통해 비슷한 유형의 변수를 쉽게 만들 수 있습니다.

```ts
// IPerson_ICompany.ts
export interface IPerson {
  name: string;
  age: number;
}

export interface ICompany {
  name: string;
  age: number;
}

// import
import { IPerson, ICompany } from "./IPerson_ICompany";

let jack: IPerson = { name = "Jack", age: 32 };
let jane: IPerson = { name = "Jane", age: 30 };

let apple: ICompany = { name = "Apple Inc", age: 43 };
let ms: ICompany = { name = "ms Inc", age: 40 };
```

#### 비구조화

- 구조화된 데이터를 어떤 시점에선 데이터의 일부만 사용해야 할 때 분해하는 것을 비구조화라고 합니다.

```ts
let name = jack.name;
let age = jack.age;
```

#### 비구조화 할당

- 객체에서 얻고 싶은 속성을 중괄호로 묶습니다.
  배열, 튜플에서도 적용할 수 있습니다.

```ts
let { name, age } = jack;
```

### 잔여 연산자 ( rest 연산자)

- 점을 연이어 3개 `...`연산자를 제공합니다.
  사용하는 위치에 따라 잔여 연산자 혹은 전개 연산자라고 불립니다.

### 전개 연산자 ( spread 연산자)

- `...` 키워드

### 3-5. 객체의 타입 변환

#### 타입 변환

- 특정 타입의 변숫값을 다른 타입의 값으로 변환할 수 있는 기능입을 타입 변환이라고 합니다.
- 당연히도 타입이 다르면 오류지만 일시적으로 타입을 변환합니다.

```ts
let person : object = {name: 'Jack', age: 32}
person.name    // error

// 타입 변환
(<{name: string>)person.name    // OK
```

#### 타입 단언

- 타입스크립트에서 사용하는 타입 변환을 JS와 구분하기 위해 타입 단언이라는 용어를 사용합니다.

```TS
(<타입>객체)
(객체 as 타입)
```

```ts
interface INameable {
  name: string;
}

let obj: object = { name: "Jack" };

let name1 = (<INameable>obj).name;
let name2 = (obj as INameable).name;
```
