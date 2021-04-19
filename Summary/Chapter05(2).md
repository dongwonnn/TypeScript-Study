### 5-2. 선언형 프로그래밍과 배열

- 함수형 프로그래밍은 **선언형 프로그래밍**과 깊은 관련이 있습니다. **배열**은 선언형 프로그래밍을 구현할 때 필수적인 문법 입니다.

- **선언형** 프로그래밍 **( <= 함수형 프로그래밍 )**
  1. 인간에게 친화적인 고수준 구현 방식
  2. 일괄된 문제 해결 구조에 집중
  3. **모든 데이터를 배열에 담아 또 다른 형태의 배열로 가공하는 방식**. 배열 메서드를 이용
- **명령형** 프로그래밍
  1. CPU에 친화적인 저수준 구현 방식
  2. 시스템 자원의 효율적인 운용
  3. **`for` 문을 이용해 데이터에 접근, 가공**

### 5-3. 배열의 map, reduce, filter 메서드

- map : 배열 데이터 가공하기
- reduce : fold 함수. ( 데이터를 가공해 하나의 값 생성 )
- filter : 조건에 맞는 아이템만 추려내기

### 5-4. 순수 함수와 배열

- 함수형 프로그래밍은 `순수 함수`의 조건을 만족해야 합니다.
  배열 클래스는 순수 함수 조건에 부합하지 않는 메서드가 많아 고려해서 사용해야 합니다.

#### 순수 함수란?

- `부수 효과`가 없는 함수, 즉 함수의 고유한 목적 이외에 다른 효과가 나타나지 않는 함수를 말합니다. 반대 함수는 `불순 함수`라고 합니다.

- 순수 함수가 아닌 예

```ts
// 매개 변수로 전달받은 array 배열 변경
function A(array: number[]): void {
  array.push(1);
}
```

```ts
// 함수 내부에 외부 변수 사용
let g = 0;
function B(x: number) {
  return x + g;
}
```

#### 타입 수정자 readonly

- TS는 순수 함수 구현을 쉽게 하도록 `readonly` 키워드를 제공합니다. `readonly`는 선언된 매개변숫값을 변경하려는 시도가 있으면 오류를 발생시켜 방지합니다.
  cf) `const`가 있는데 `readonly`가 있는 이유는 인터페이스, 클레스, 함수의 매개변수 등은 `const` 키워드를 사용할 수 없어 이 때 `readonly`를 사용하면 됩니다.

#### 불변과 가변

- `const`나 `readonly`를 명시하고 있으면 변숫값은 초기값을 항상 유지합니다. 이것을 **불변 변수**라고 합니다. 반대를 **가변 변수**라고 합니다.

#### 깊은 복사와 얕은 복사

- 어떤 변숫값을 다른 변숫값으로 설정하는 것을 **복사**라고 합니다. 복사는 **깊은 복사**와 **얕은 복사**가 있습니다.
- 순수 함수를 구현할 때 매개변수가 **불변성**을 유지해야 하므로 **깊은 복사**를 이용하여 매개변숫값이 변경되지 않도록 해야합니다.

- **깊은 복사**는 원본 변숫값은 그대로인 형태로 동작해야 합니다.

```ts
// 깊은 복사 예
let origin = 1;
let copied = orgin;
let copied++;

// origin : 1
// copied : 2
```

- 이러한 방법은 객체나 배열에서는 얕은 방식으로 동작합니다.
  객체나 배열에서 깊은 복사를 하기 위해서 `전개 연산자`를 사용합니다.

```ts
const origin = [1, 2, 3, 4];
const copied = [...orgin];
copid[0] = 0;
// origin = [1,2,3,4]
// copied = [0,2,3,4]
```

#### 배열의 sort 메서드를 순수 함수로 구현하기

- 정렬의 기능이 있는 `sort`메서드는 원본 배열의 내용을 변경합니다. 이것을 해결하기 위해 `readonly` 타입으로 입력 배열의 내용을 유지한 채 정렬할 수 있도록 `전개 연산자`의 깊은 복사 기능을 사용할 수 있습니다.

```ts
export const pureSort = <T>(array: readonly T[]): T[] => {
  let deepCopied = [...array];
  return deepCopied.sort();
};

// array = [6,2,9,0]
// deepCopied = [0,2,6,9]
```

#### 배열의 filter 메서드와 순수한 삭제

- 배열의 특정 아이템을 삭제하기 위해 `splice` 메서드를 사용합니다. 하지만 `splice` 또한 배열의 내용을 변경시키므로 순수 함수의 조건에 맞지 않습니다.
  하지만 `filter` 메서드는 깊은 복사 형태로 동작하여 순수 함수의 조건으로 조건에 맞지 않는 아이템을 삭제할 수 있습니다.

```ts
const pureDelete = <T>(
  array: readonly T[],
  cb: (val: T, index?: number) => boolean
): T[] => array.filter((val, index) => cb(val, index) === false);

const mixedArray: object[] = [[], { name: "Jack" }];
// pureDelete(mixedArray, (val)=> Array.isArray(val))
// {name : 'Jack'}
```

#### 가변 인수 함수와 순수 함수

- 함수를 호출할 때 인수의 개수를 제한하지 않는 것을 **가변 인수**라고 하고 이런 방식으로 동작하는 함수를 **가변 함수**라고 합니다..

```ts
// 인수의 개수가 다르다
const mergeArr1: string[] = mergeArray(["1"], ["2"]);
const mergeArr2: number[] = mergeArray([1], [2, 3], [4]);
```

- 다음의 `(...array)`는 `잔여, 전개 연산자`가 아닌 `가변 인수`를 표현하는 구문입니다.
- 배열 인수 여러개를 전달하므로 배열의 배열 형태(`T[][]`)로 입력 배열을 설정합니다.
  출력은 배열 하나므로 `T[]`, 순수 함수를 위해 `readonly`를 설정합니다.

```ts
const mergeArray = <T>(...arrays: readonly T[][]): T[] => {};
```

#### 배열의 메서드 중 순수함수가 아닌 메서드들을 전개 연산자와 같은 방식으로 순수 함수 형태로 구현해 사용할 수 있습니다.

### 5-5. 튜플 이해하기

- 여러 타입에 대응하는 `any 타입 배열`로 선언할 수 있거나 대응하는 타입으로 설정할 수 있습니다.

```ts
let tuple: any[] = [true, "abc"];
let tuple2: [boolean, string] = [true, "abc"];
```

#### 튜플에 타입 별칭 사용하기

- 보통 튜플을 사용할 때 `any`보단 **타입 별칭**으로 튜플의 의미를 명확하게 합니다.

```ts
type ResultType = [boolean, string];

const doSomething = (): ResultType => {
  try {
    throw new Rrror("error");
  } catch (e) {
    return [false, e.message];
  }
};
```

#### 튜플에 적용하는 비구조화 할당

- `튜플`은 물리적으로 `배열`이므로 `비구조화 할당`을 적용할 수 있습니다.

```ts
const [result, errorMessage] = doSomething();
```
