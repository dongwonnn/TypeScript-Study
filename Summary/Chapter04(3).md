## 함수와 메서드(3)

### 4-5. 함수 구현 기법

#### 매개변수 기본값 지정하기

- 선택적 매개변수는 기본 값이 undefined 입니다. 만일 함수 호출 시 인수를 전달하지 않아도 매개변수에 어떤 값을 설정하고 싶다면 **디폴트 매개변수**를 이용해 기본값을 지정할 수 있습니다.

```ts
const makePerson = (name: string, age: number = 10): Person => {};
```

#### 객체 생성 시 값 부분을 생략할 수 있는 TS 구문

```TS
const makePerson = (name : string, age: number) => {
    const person = {name, age} // {name: name, age: age}
```

#### 객체를 반환하는 화살표 함수 만들기

- 중괄호 `{}`를 사용하되 복합 실행문이 아닌 객체로 해석하기 위해 **소괄호 `()`로 묶습니다**.

```ts
const makePerson = (name: string, age: number = 10): Person => ({ name, age });
```

#### 매개변수에 비구조화 할당문 사용하기

- 객체의 비구조화 할당문과 같이 **함수의 매개변수도 변수므로 비구조화 할당문**을 적용할 수 있습니다.

```ts
type Person = { name: string; age: number };

const printPerson = ({ name, age} : Person ) void => {}
```

#### 색인 키와 값으로 객체 만들기

- ESNext 에서는 다음과 같이 객체를 만들 수 있습니다.
  `const makeObject = (key, value) => ({[key] : value})`
- TS에서는 `{[key]: value}`와 같은 형태를 **색인 기능 타입**이라고 하고 key와 value의 타입을 명시합니다.

```ts
type keyType = {
  [key: string]: string; // key : value
};

const makeObject = (key: string, vlaue: string): keyType => ({ [key]: value });

makeObject("name", "Jack"); // name : 'Jack'
makeObject("abc", "Jack"); // abc : 'Jack'
```

### 4-6. 클래스와 메서드

#### function 함수와 this 키워드

- TS의 함수는 Function 클래스의 인스턴스, 객체입니다. 객체 지향언어에서 인스턴스는 this를 이용할 수 있습니다.
  cf) 화살표 함수는 this 사용 불가능

#### 메서드란?

- TS에서 메서드는 **function으로 만든 함수 표현식을 담고 있는 속성**입니다.
  다음은 **값**과 **함수 표현식의 속성**을 가진 클래스 입니다.

```ts
class A {
  value: number = 1;
  method: () => void = function (): void {
    consoe.log(`value : ${this.value}`);
  };
}
```

- 주목해볼만한 것은 `this` 입니다. `this.value`를 통해 같은 클래스 내의 변수에 접근할 수 있습니다.

```ts
let a: A = new A();
A.method(); // valeu : 1
```

#### 클래스 메서드 구문

- TS는 클래스 속성 중 함수 표현식을 담는 속성은 `function` 키워드를 삭제할 수 있게 하는 단축 구문을 제공합니다.

```ts
class B {
  constructor(public value: number = 2) {}
  method(): void {
    cosnole.log(`value : ${this.value}`);
  }
}
```

```ts
let b: B = new B();
B.method(); // value : 2
```

#### 정적 메서드

- 클래스의 속성은 `static`을 붙여 **정적**으로 만들 수 있습니다.

```ts
class C {
  static whoAreYou(): string {
    return "C";
  }
}

class D {
  static whoAreYou(): string {
    return "D";
  }
}

C.whoAreYou(); //  C
D.whoAreYou(); //  D
```

#### 메서드 체인

- 객체의 메서드를 이어서 계속 호출하는 방식의 코드를 **메서드 체인**이라고 합니다.
- TS로 메서드 체인을 구현하려면 메서드가 항상 `this`를 반환하게 합니다.

```ts
class Calc {
  // value : number
  constructor(public value : number)
  add(value : number){
    this.value += value
    return this
  }
  mult(value: number){
    this.value *= value
    return this
  }
}

let calc : new Calc
let result = calc.add(1).add(2).mult(3)..
```
