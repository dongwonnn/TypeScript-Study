## 객체와 타입(2)

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
