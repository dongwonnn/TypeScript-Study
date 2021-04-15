## 타입스크립트와 개발 환경 만들기

### 1-1. 타입스크립트란 무엇인가 ?

#### 세 종류의 자바스크립트

1. ES5
2. ESNext (ES6+)
3. TypeScript ( ESNext에 type 추가 )

#### JS에 type이 있으면 좋은 이유

JS의 기능적 문제로 인한 오류의 원인을 알 수 있어 코드를 수월하게 작성 가능하다.
이러한 이점 때문에 대규모 소프트웨어 개발에 이용된다.

#### 트랜스파일

- 트랜스파일 : 어떤 프로그래밍 언어로 작성된 소스코드를 또 다른 프로그래밍 언어로 된 소스코드로 바꿔주는 프로그램. 컴파일러와는 다르다.
  1. 바벨 : ESNext를 ES5로 변환.
  2. TSC : TypeScript를 ES5로 변환.

### 1-2. 타입스크립트 주요 문법

- 타입스크립트는 ESNext 문법을 지원한다.

#### ESNext의 주요 문법

- 비구조화 할당
- 화살표 함수
- 클래스 : 객체 지향 프로그래밍 지원.
- 모듈 : 코드를 여러 개 파일로 분할하여 작성 가능.
  `export ` `import` 키워드
- 생성기(`generate`) : `yield`문을 이용해 반복기를 만들어 내는 반복기 생성자
  `yield`: 반복자, `iterator`: 반복기, `iterable` : 반복기 제공자
  `yield`는 `function*` 안에서만 사용 가능.
- Promise와 async/await 구문
  콜백 지옥 해결하기 위해 비동기 콜백함수를 상대적으로 쉽게 구현할 목적으로 만든 구문.
  async/await 구문을 사용해 Promise의 복잡한 형태를 간결하게 구현.
  `async` : 함수 수정자
  `await` : `async` 함수 본문에서 사용가능. Promise 객체를 resolve 해 준다. 따라서 then을 이용해 값을 얻을 수 있다.

#### 타입스크립트 고유의 문법

- 타입 주석과 타입 추론
  1. 타입 주석 : 콜론(:)과 타입 이름
     `let n : number = 1`
  2. 타입 추론 : 타입 주석은 생략 가능하다. = 연산자의 오른쪽 값을 분석해 왼쪽 변수의 타입을 결정할 수 있다. 이 기능으로 인해 확장자 js를 ts로 바꿔도 타입스크립트 환경에서 바로 동작 가능하다.
     `let m = 2`
- 인터페이스

  ```js
  interface Person {
    name: string;
    age?: number;
  }

  let person: Person = { name: "Jane" };
  ```

- 튜플
  물리적으론 배열과 같다. 배열에 저장되는 아이템의 데이터 타입이 모두 같으면 배열, 다르면 튜플.
  `let array : number[] = [1,2,3] `
  `let tuple : [boolean, number, string] = [true, 1, 'OK']`
- 제네릭 타입
  다양한 타입을 한꺼번에 취급할 수 있게 해준다. number, string, array, boolean 등..

  ```js
  class Container<T> {
      constructor(public value: T) {}
  }

  let numberContainer : Container<number> = new Container<Number>(1)
  let stringContainer : Container<string> = new Container<Number>('hello')
  ```

- 대수 타입 (ADT)
  다른 자료형의 값을 가지는 자료형을 의미한다.
  1. 합집합 타입 `type numberOrString = number | string`
  2. 교집합 타입 `type AnimalAndPerson = Animal & Person`

### 1.3 개발환경 만들기

#### vscode, node.js

#### 타입스크립트 컴파일러 설치

- `npm i -g typescript`
- hello.ts에 `console.log('hello')`를 치고 터미널에서 `tsc hello.ts`를 입력하면 hello.js 생성 (tsc가 ES5 형식의 코드로 변환)
- 이후에 node hello.js를 실행시키면 실행된다.

#### ts-node 설치

- 타입스크립트 코드를 ES5로 변환하고 실행까지 동시에 할 수 있다.
- `npm i -g ts-node`
- `ts-node hello.ts`
