## 배열과 튜플

### 5-1. 배열 이해하기

- JS에서 배열은 `Array` 클래스의 인스턴스이며 다음과 같이 선언합니다.
  push 메서드를 이용해 값을 추가할 수 있습니다.

```ts
let array = new Array();
array.push(1);
array; // [1]
```

#### [] 단축 구문

- 간편하게 `array`를 선언할 수 있습니다.

```ts
let array = [1, 2, 3];
```

#### 자바스크립트에서 배열은 객체다.

- 배열은 Array의 인스턴스이며 클래스의 인스턴스는 객체입니다.
- `Array.isArray`를 통해 매개변수로 전달받은 심벌이 배열임을 판별할 수 있습니다.

```ts
let array = [];
Array.isArray(array); // true
```

#### 배열의 타입

- TS에서 배열의 타입은 `아이템 타입[]`입니다.
  다음은 배열에 타입 주석을 붙이는 방법입니다.

```ts
let numArr: number[] = [1, 2, 3];
let strArr: string[] = ["a", "b", "c"];

type IPerson = { name: string; age?: number };
let personArray: IPerson[] = [{ name: "Jack" }, { name: "Jane", age: 32 }];
```

#### 문자열과 배열 간 변환

- 어떤 프로그래밍 언어에선 문자열을 문자들의 배열로 간주합니다.
  TS에선 문자 타입이 없고 문자열의 내용 또한 변경할 수 없어 문자열을 가공하려면 문자열을 배열로 변환해야 합니다.
- 문자열을 배열로 전환할 때 String 클래스의 `split` 메서드를 사용합니다.
  split에서는 문자열을 문자로 쪼개는 `구분자(delimiter)`를 입력받아 String[] 배열로 만들어 줍니다.

```ts
const split = (str: string, delim: string = ""): string[] => str.split(delim);

split("hello"); // ['h','e','l','l','o']
```

- 다시 결합하려면 `join` 메서드를 사용합니다.

```ts
const join = (strArr: string[], delim: string:''): string => strArr.join(delim)

join(['h','e','l','l','o']) // hello
```

#### 인덱스 연산자

- 배열의 특정 위치의 아이템을 얻고 싶을 때 `인덱스 연산자 []`를 사용합니다.

```ts
let item: number = [0, 1, 2, 3, 4];
let result = item[3]; // 3
```

#### 배열의 비구조화 할당

- 객체 뿐 아니라 배열도 비구조화 할당을 할 수 있습니다.

```ts
let array: number[] = [1, 2, 3, 4];
let [first, second, ...rest] = array;
first: 1;
second: 2;
rest: [3, 4];
```

#### for ...in 문

- `for in`문은 객체를 대상으로 하지만 배열도 객체이므로 둘다 사용 가능합니다.
  배열의 인덱스 값으로 순회합니다.

```ts
// 배열에서
let arr = ["a", "b", "c"];
for (let idx in arr) {
  const alp = arr[idx]; // 'a','b','c'
}
```

- 객체의 경우 객체의 속성을 기준으로 작동합니다.

```ts
let jack = { name: "Jack", age: 32 };
for (let property in jack) {
  console.log(`${property} : ${jack[property]}`); // name: Jack age: 32
}
```

#### for ..of 문

- `for of`문은 배열의 아이템값을 대상으로 순회합니다.

```ts
for (let name of ["kim", "lee", "park"]) {
  console.log(name); // kim lee park
}
```

#### 제네릭 방식 타입

- 배열을 다루는 함수를 작성할 때 `number[], string[]`과 같이 고정된 타입보단 T[] 형태로 배열의 아이템 타입을 한꺼번에 표현할 수 있습니다. 타입 T와 같은 타입을 **제네릭 타입**이라고 합니다.

- 다음 함수는 배열의 길이를 얻는 함수입니다.

```ts
const arrLength = (array) => array.length;
```

- 이 함수가 다양한 타입의 배열에 같이 적용되기 위해선 **타입 주석을 T[]**로 설정해야 합니다.

```ts
const arrLength = (array: T[]) => array.length;
```

- 이 과정에서 컴파일러가 `T`의 의미를 알아야 합니다. 즉, `<T>`를 사용하여 `T`가 `타입변수`라고 알려줘야 합니다

```ts
const arrLength = <T>(array: T[]) => array.length;

let numArr: number[] = [1, 2, 3];
let strArr: string[] = ["a", "b", "c", "d"];

arrLength(numArr); // 2
arrLength(strArr); // 3
```

#### 제네릭 함수의 타입 추론

- 타입 변수 부분을 추론하여 생략이 가능하게 합니다.

```ts
//const 함수이름 = <타입변수>매개변수;
const identity = <T>(n: T): T => n;
const identity2 = <T>: T= > n;
```

#### 제네릭 함수의 함수 시그니처

- 어떤 경우에 함수 시그니처의 매개변수 부분에 변수 이름을 기입하라고 요구합니다.
  이처럼 TS가 해석하지 못하는 부분ㅇ 변수를 삽입하고 이 변수에 타입을 명시에 해결합니다.

```ts
const error = (cb: (number, number?) => number): void => {};
const fixed = (cb: (a: number, number?) => number): void => {};
```

- 이 문제는 제네릭 타입의 함수에서도 같은 문제가 발생합니다. `fixed`처럼 동일하게 해결합니다.

```ts
const f = <T>(cb: (arg: T, i?: number) => number): void => {};
```

#### 전개 연산자

- 전개 연산자는 배열에도 적용할 수 있습니다. 두 배열의 특정 값을 동시에 결합할 수 있습니다.

```ts
let arr1 = [1, 2, 3];
let arr2 = [4, 5, 6];
let mergetArr = [...arr1, ...arr2]; // [1,2,3,4,5,6]
```

#### range 함수 구현

- 전개 연산자를 이용해 `ramda` 외부 패키지가 제공하는 `R.range`를 구현할 수 있습니다. 재귀함수를 이용했습니니다.

```ts
const range = (from : number, to : number) : number[] => {
    from < to ? [from, ...[rage(from+1, to)]]
}

let number : number[] = range(1, 9+1)   // [ 1 ~ 10 ]
```
