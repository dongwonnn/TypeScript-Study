## 함수와 메서드(2)

### 4-3. 화살표 함수와 표현식 문

- ESNext에서는 `function` 키워드가 아닌 `=>` 기호로 만다는 화살표 함수도 제공합니다.
- 화살표함수는 중괄호 사용여부에 따라 문법이 동작하는 방식이 `실행문` 인지 `표현식 문` 인지로 나눠 집니다.

#### 실행문과 표현식 문

- ESNext는 실행문과 표현식 문을 동시에 지원하는 **다중 패러다임 언어**입니다.
- 실행문
  CPU에서 실행되며 결과값을 알려주지 않습니다. `return` 키워드를 이용하여 알 수 있습니다.
- 표현식 문
  CPU에서 실행된 결과를 `return`키워드를 사용하지 않아도 알려줍니다.
  표현식 문을 사용하면 결과값을 알아야하는 코드에 return 키워드를 사용하지 않아도 되어 코드 작성하기가 수월해집니다.
  x가 0보다 큰지 true/false 값이 return 되어야 하지만, 표현식 문을 사용하여 `return` 키워드 없이 true/false를 알 수 있습니다.

```ts
let x = 10;
if (x > 0) x = 1;
```

#### 복합 실행문

- 중괄호 `{}`를 이용하여 여러 실행문을 한 개처럼 인식하게 합니다.

```ts
if (조건식) {
  실행문1;
  실행문2;
}
```

#### return 키워드

- 실행문에서 결과값을 알기위해 사용합니다. `return` 키워드는 반드시 함수의 몸통 안에서 사용 가능합니다.

```ts
function isGenerator(a: number, b: number): boolean {
  return a > b;
}
```

#### 표현식 문 스타일의 화살표 함수 구현

- `{}`와 `return` 생략

```ts
const isGenerator = (a: number, b: number): boolean => {
  return a > b;
};

// 다음과 같다
const isGenerator = (a: number, b: number): boolean => a > b;
```

### 4.4 일등함수

#### 콜백함수

- 일등 함수 기능을 제공하는 언어에서 함수는 '함수 표현식'이라는 값입니다.
  변수에 담을 수 있습니다. 즉, 함수 표현식을 매개변수로 받을 수 있습니다.
  이 처럼 매개변수 형태로 동작하는 함수를 **콜백함수**라고 합니다.

```ts
// JS : const f = (callback) => callback() ;
const f = (callback: () => void): viod => callback();
```

- 함수 `()=> console.log("2")` 자체를 callback 이라는 이름으로 init() 함수에 매개변수로 넘깁니다.

```ts
// TS
export const init = (callback: () => void): void => {
  console.log("1");
  callback();
  console.log("3");
};

init(() => console.log("2")); // 1 2 3

// JS
const init = (callback) => {
  console.log("1");
  callback();
  console.log("3");
};

init(() => console.log("2"));
```

#### 중첩 함수

- 함수형 언어에서 함수는 변수에 담긴 함수 표현식이므로 함수 안에 **또 다른 함수를 중첩**해서 구현할 수 있습니다.

```ts
const calc = (value: number, cb: (number) => void ) : void => {
    let add = (a,b) => a+b
    function multiply(a,b) {return a*b}

    // 함수 중첩
    let result = multiply(add(1,2), value)
    cb(result)
}
calc(30, (result:number => console.log('check')))

// JS
const calc = (value, cb) => {
    let add = (a,b) => a+b
    function multiply(a,b) {return a*b}

    let result = multiply(add(1,2), value)
    cb(result)  // 90
}
calc(30, () => console.log(`${result}`)))
```

#### 고차함수와 클로저, 부분함수

- **고차함수**는 또 **다른 함수를 반환**하는 함수를 말합니다.
  함수형 언어에서 함수는 단순히 함수 표현식이라는 값이므로 다른 함수를 반환할 수 있습니다. -> 함수형 프로그래밍에서 매우 중요한 기능

```ts
const add1 = (a: number, b: number): number => a + b; // 일반 함수
const add2 = (a: number): ((number) => number) => (b: number): number => a + b; // 고차 함수

//JS
const add1 = (a, b) => a + b;
const add2 = (a) => (b) => a + b;
```

- 고차함수 호출 방법

```ts
const add = (a: number): ((number) => number) => (b: number): number => a + b; // 고차 함수
const result = add(1)(2); // 3

// JS 풀어쓴 코드
const add = (a) => {
  (b) => {
    return a + b;
  };
};
```

- 쉬운 형태로 다시 구현
  1. number 타입의 매개변수, number 타입의 값을 반환하는 함수 시그니처 NumberToNumberFunc
  2. NumberToNumberFunc 타입의 함수 반환하는 add 함수
  3. add 반환값을 중첩 함수로 구현
  4. \_add 몸통 구현 = **클로저**

```ts
type NumberToNumberFunc = (number) => number;

export const add = (a: number): NumberToNumberFunc => {
  const _add: NumberToNumberFunc = (b: number): number => {
    return a + b; // 클로저
  };
  return _add;
};

// JS
const add = (a) => {
  const _add = (b) => {
    return a + b;
  };
  return _add;
};
```

- 위 코드에서 `a`는 `add`의 매개 변수, `b`는 `_add`의 매개 변수 입니다. 즉 `_add`입장에서 `a`는 외부에서 선언된 변수입니다. 이러한 형태를 **클로저**라고 합니다.

```ts
let fn: NumberToNumberFunc = add(1); // a = 1
// b = 3
let result = fn(2); // = add(1)(2);
```

- 만약 3차 고차 함수로 구현되어 있다면, `mult(1)(2)(3)` 처럼 호출 연산자가 3개 필요합니다.
  만약 `mult(1)(2)`만 사용한다면 아직 값이 아닌 함수입니다. 이것을 **부분 적용 함수** 혹은 **부분 애플리케이션** 이라고 합니다.
