## Promise와 async/awit 구문

### 7-1. 비동기 콜백 함수

#### 실습 프로젝트 설정

```ts
//tsconfig.json
"downlevelIteration" : true
```

#### 동기와 비동기

- **동기 방식**
  1. 운영체제는 동기함수를 만나면 함수의 **반환값**을 돌려주어야 합니다.
  2. 이 때 **단일 스레드**로 동작하는 JS에서 반환값을 받기 까지 프로그램의 **동작을 잠시 멈춥니다**.
  3. 동기 방식은 코드를 작성하기 쉽지만 프로그램의 **반응성**을 떨어뜨립니다.
- **비동기 방식**
  1. 특정 동작을 할 때 프로그램 동작을 멈추는 대신 **결과를 콜백 함수로 엊게해** 프로그램이 멈추지 않고 동작합니다.
  2. 비동기 함수의 콜백함수를 **비동기 콜백 함수**라고 합니다.
  3. 이 때문에 코드가 복잡해지지만 **반응성**을 훼손하지 않습니다.

#### 콜백지옥

- 비동기 API를 사용하면 콜백 함수에서 또 다시 비동기 함수를 만들때 코드가 복잡해집니다. 중첩에 중첩이 되어 복잡한 형태로 얽힌 콜백 구조를 **콜백 지옥**이라고 합니다.

```TS
function firstCB(){
    function secondCB(){
        ...
    }
}
```

- `Promise`는 이러한 콜백지옥을 해결하기 위해 만들어졌습니다.

### 7-2. Promise 이해하기

- JS에서 프로미스는 Promise라는 이름의 클래스입니다. `new`연산자를 이용해 **프로미스 객체**를 만들고 **콜백 함수**를 제공해야 합니다.

```ts
const promise = new Promise(콜백 함수)
```

- 여기서 `Promise`는 `resolve`와 `reject` 두 개의 매개변수를 가집니다.
  `(resolve, reject) => {}`

- TS에서 Promise는 제네릭 클래스 형태로 사용합니다.

```ts
const promise : Promise<T> = new Promise<T>(콜백 함수)
```

- TS에서 `Promise` 콜백 함수는 `resolve`와 `reject` 함수를 매개변수로 받는 형태입니다.

```ts
new Promise<T>((resolve: (successValue: T) => void, reject: (any) => void) => {
  // 코드 구현
});
```

#### Promise 이용한 비동기 함수 구현

```ts
const readFilePromise = (filename: string): Promise<string> =>
  new Promise<string>((
      resolve: (value: string) => void,
      reject: (error: Error) => void) => {
      readFile(filename, (err: Error, buffer: Buffer) => {
        if (err) reject(err);
        else resolve(buffer.toString());
      });
    }
  );

// JS
const readFilePromise1 = (filename) =>
    new Promise(resolve, reject) => {
        readFile(filename, (err, buffer)) => {
            if(err) reject(err);
            else resolve(buffer.toString())
        }
    }
```

- 함수 사용 코드
  then, catch, finally 메서드를 사용해 체인 형태로 사용합니다.
- `readFile`에서 `resolve` 함수는 `.then`메서드의 콜백 함수로, `reject` 함수는 `.catch`메서드의 콜백 함수 쪽으로 전달됩니다. 마지막은 `finally`가 호출 됩니다.

```ts
readFilePromise("./package.json")
  .then((content: string) => {  // content === value
    console.log(content); // package.json 파일의 내용
    return readFilePromise("./tsconfig.json");
  })
  .then((content: string) => {
    console.log(content); // tsconfig.json 파일의 내용
    return readFilePromise("."); // '.'이 없어서 오류 발생 -> catch
  })
  .catch((err: Error) => console.log('error : ' : err.message))
  .finally(()=> console.log('종료'))
```

#### Promise.resolve 메서드

- Promise 클래스는 `resolve` 클래스 메서드를 제공합니다. `Promise.resolve(값)` 형태로 호출하면 항상 이 '값'은 `then` 메서드에서 얻을 수 있습니다.

```ts
Promise.resolve(1).then((value) => console.log(value)); // 1
```

#### Promise.reject 메서드

- `Promise.reject(Error 타입 객체)`를 호출하면 항상 `catch` 메서드의 콜백 함수에서 얻을 수 있습니다.

```ts
Promise.reject(new Error('에러 발생')).catch((err:Error)=> console.log('error' : err.message))
```

#### then-체인

- `Promise`의 `then` 인스턴스 메서드를 호출할 때 사용한 콜백 함수는 `return`을 이용해 값을 반환할 수 있습니다. 이 `then`에서 반한된 값은 또 다른 `then`메서드를 호출해 값을 수신할 수 있습니다.
  반한된 값이 `Promise` 타입이면 이를 `resolve`한 값을 반환합니다.
  `Promise` 객체에 `then` 메서드를 여러 번 호출하는 코드 형태를 **then-체인**이라고 합니다.

```ts
Promise.resolve(1)
  .then((value: number) => {
    // 1
    return Promise.resolve(true);
  })
  .then((value: boolean) => {
    // true
    return Promise.resolve([1, 2, 3]);
  })
  .then((value: number[]) => {
    console.log(value); // [1,2,3]
  });
```

#### Promise.all 메서드

- `promise.all` 메서드는 `Promise` 객체들을 배열 형태로 받아 모든 객체를 대상으로 `resolve` 된 값을의 배열로 만들어 줍니다.

```ts
const getAllResolvedResult = <T>(promise: Promise<T>[] => Promise.all(promises)

getAllResolvedResult<any>([Promise.resolve(true), Promise.resolve('hello')])
.then(result=> console.log(result)) // [true, hello]
```

#### Promise.race 메서드

- 배열에 담긴 프로미스 객체 중 하나라도 `resolve`되면 이 값을 담은 `Promise.resolve` 객체를 반환합니다.
  뒤 아이템은 앞 아이템이 반환되면 무시됩니다.

```ts
Promise.race([Promise.resolve[true], Promise.resolve("hello")]).then(
  (value) => console.log(value) // true
);
```

### 7-3. async와 await 구문

#### await 키워드

- `await` 키워드는 피연산자의 값을 반환해 줍니다. 만일 피연산자가 `Promise` 객체이면 `then` 메서드를 호출해 얻은 값을 반환해 줍니다.

```ts
let value = await Promise 객체 or 값
```

#### async 함수 수정자

- `await` 키워드는 `async` 라는 이름의 **함수 수정자**가 있는 함수 몸통에서만 사용할 수 있습니다.

```ts
const test1 = async () => {
  let value = await 1;
  console.log(value);
  let value = await Promise.resolve(1);
  console.log(value);
};

const test2 = async () => {
  let value = await "hello";
  console.log(value);
  let value = await Promise.resolve("hello");
  console.log(value);
};
```

- `async`는 함수 이므로 일반 함수처럼 호출이 가능합니다.

```ts
test1();
test2();

// 1
// hello
// 1
// hello
```

#### async함수의 두 가지 성질

- `async` 함수의 성질
  1. 일반 함수 처럼 사용 가능하다.
  2. Promise 객체로 사용할 수 있다. -> then 등 사용가능
- 위의 결과는 `test1`과 `test2`가 동시에 실행되는 것 처럼 보입니다.
  `async`의 두 번째 성질을 이용해 분리할 수 있습니다.

```ts
test1().then(() => test2());

// 1
// 1
// hello
// hello
```

#### async 함수가 반환하는 값의 의미

- `async` 함수는 값을 반환할 수 있습니다. 반환값은 `Promise` 형태로 변환되어 `then` 메서드를 호출해 `async` 함수의 반환값을 얻어야 합니다.

```ts
const asyncReturn = async () => {
  return [1, 2, 3];
};
asyncReturn().then((value) => console.log(value)); // [1,2,3]
```

#### async 함수의 예외 처리

- `async` 함수에서 예외가 발생하거나, `await` 구문에서 `Promise.reject` 값이 발생하면 **프로그램이 비정상 종료** 합니다.
- 프로미스 객체의 `catch` 메서드를 호출해 방지할 수 있습니다.

```ts
const asyncException = async () => {
  //throw new Error("error");
  await Promise.reject(new Error("error"));
};
asyncException().catch((err) => console.log(err.message)); // error
```

#### async 함수와 Promise.all

--
