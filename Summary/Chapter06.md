## 반복기와 생성기

### 6-1. 반복기 이해하기

- 프로젝트 설정

```ts
// tsconfig.json
{
    "compilerOptions" : {
        ...
        "downlevelIteration" : true
    }
}
```

#### 반복기와 반복기 제공자

- `for of`문은 타입에 무관하게 배열에 담긴 값을 차례로 얻는 데 활용됩니다.
  이 구문을 보통 `반복기`라고 부릅니다.
- 반복는 다음과 같은 특징이 있습니다.

  1. `next`라는 이름의 메서드를 제공합니다.
  2. `next` 메서드는 `value`와 `done` 두 개의 속성을 가진 객체를 제공합니다.

  - `value`는 배열의 값
  - 배열의 마지막의 다음 즉, `value`가 `undefiend`라면 `done`

- 다음 `createRangeIterator` 함수는 `next` 메서드가 있는 객체를 반환하므로 이 함수는 **반복기**를 제공하는 함수입니다. 이것을 **반복기 제공자**라고 합니다.

```ts
const createRangeIterator = (from: number, to: number) => {
    let currnentValue = from
    retur {
        next(){
            const value = currentValue < to ? currentValue++ : undefined
            const done = value == undefined;
            return {value, done}
        }
    }
}
```

- 다음은 `createRangeIteraotr`함수가 제공하는 **반복기**를 사용하는 예입니다.
  `next()` 메서드를 실행해야 **반복기**가 동작, 그 이전에는 **반복기**가 동작하지 않습니다.
  `next()` 메서드를 반복 동작하면서 **반복기 제공자**가 제공하는 `value`를 얻을 수 있습니다.

```ts
const iterator = createRangeIterator(1, 3 + 1);
while (true) {
  const { value, done } = iterator.next();
  if (done) breack;
  console.log(value); // 1 2 3
}
```

#### 반복기가 필요한 이유

- **반복기**는 어떤 범위의 값을 한꺼번에 배열에 담지 않고 **필요할 때**만 생성이 가능합니다. 즉, `next()`메서드를 **호출할때** 값을 생성하지만 반복기를 사용하지 않은 함수는 값이 필요한 시점 이번에 **미리** 생성하는 차이가 있습니다. 때문에 반복기를 사용하면 **메모리를 훨씬 적게 소모**할 수 있습니다.

#### for of 구문과 [Symbol.iterator] 구문

- **반복기 제공자**함수를 `for of`문에 실행시키면 `[Symbol.iterator]()` 메서드가 없다는 오류를 발생시킵니다.
  이 오류는 **반복기 제공자**함수를 class로 생성해 `[Symbol.iterator]()` 메서드를 구현하라는 메세지 입니다.

```ts
class Rangeiteralbe {
  constructor(public from: number, to: number) {}
  [Symbol.iterator]() {
    // next()의 this와 구분하기 위한 that
    const that = this;
    let currentValue = that.from;
    return {
      next() {
        const value = currentValue < that.to ? currentValue++ : undefined;
        const done = value == undefined;
        return { value, done };
      },
    };
  }
}
```

#### iterable<T>와 iterator<T>

- TS는 **반복기 제공자에** `iterable<T>`와 `iterator<T>` 제네릭 인터페이스를 제공할 수 있습니다.
  `iterable<T>`는 자신을 구현하는 클래스가 `[Symbol.iterator]`를 제공한다,
  `iterator<T>`는 반복기가 생성할 **값의 타입**을 명확하게 해줍니다.

```ts
class StringIterable implements Iterable<string> {
    ...
}
```

### 6-2. 생성기 이해하기

- ESNext JS 에서는 `yield`라는 키워드를 제공합니다. `yield`는 `function*` 키워드를 사용한 함수에서만 호출 가능합니다.
  이렇게 `function*` 키워드로 만든 함수를 **생성기**라고 합니다.

```ts
function* generator() {
  let value = 1;
  while (value < 4) {
    yield value++;
  }
}

for (let value of generator()) {
  console.log(value); // 1 2 3
}
```

- 위의 코드는 앞의 `for of` 구문과 같은 결과를 얻을 수 있습니다.

#### setInterval 함수와 생성기의 유사성

- 생성기가 동작하는 방식을 **세미 코루틴**이라고 합니다. **단일 스레드**로 동작하는 TS가 **다중 스레드**로 동작하는 것처럼 보이게 하는 기능입니다.

- 아래 코드의 결과는 생성기 방식과 비슷하게 동작하지만 `setInterval` 함수는 c언어의 `스레드`가 동작하는 방식과 유사합니다.

```ts
const id = setInterval(() => {
  console.log("started");
  if (count >= 3) {
    clearInterval(id);
    console.log("finished");
  } else {
    console.log(++count);
  }
}, 1000);

// started
// 1
// 2
// 3
// finished
```

#### function\* 키워드

- **생성기**는 `function*` 키워드로 생성이 가능해 화살표 함수`=>`로는 만들 수 없습니다.
  **생성기**는 반복기를 제공하는 반복기 제공자로서 동작합니다.

#### yield 키워드

- `yield` 키워드는 연산자 형태로 동작하고 다음과 같은 기능을 합니다.
  1. 반복기를 자동으로 만들어준다. 즉, `next()` 메서드를 통해 `value`와 `done`을 만들어 줍니다.
  2. 반복기 제공자 역할도 수행합니다.

```ts
function* rangeGenerator(from: number, to: number) {
  let value = from;
  while (value < to) {
    yield value++;
  }
}

// 동일한 코드
function rangeIterable = (from: number, to: number){
    let currentValue = from
    return{
        next() {
            const value = currentValue < to ? currentValue++ : undefined
            const done = value == undefined
            return {value, done}
        }
}
```

#### 반복기 제공자의 메서드로 동작하는 생성기 구현

- **생성기**는 **반복기**를 제공하는 **반복기 제공자**로서 동작하므로 간결하게 구현이 가능합니다.

```ts
class IterableUsingGenerator<T> implements Iterable<T> {
    constructor(private values: T[] = [], private currentIndex: number= 0){}
    [Symbol.iterator] = function* () {
        while(this.currentIndex  this.values.length){
            yield this.values[this.currentIndex++]
        }
    }
}
```

#### yield\* 키워드

- `yiled`에 `*`를 붙인 키워드는 다른 생성기나 배열을 대상으로 동작합니다.

```ts
function* get12() {
  yield 1;
  yield 2;
}

function* gen12345() {
  yield* get12();
  yield* [3, 4];
  yield 5;
}
```

#### yield 반환값

- `yield`는 값을 반환합니다.

```ts
function* gen() {
  let count = 3;
  let select = 0;
  while (count--) {
    select = yield `you select ${select}`;
  }
}

// you select 0
// you select 1
// you select 2
```
