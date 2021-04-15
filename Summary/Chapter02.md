## 타입스크립트 프로젝트 생성과 관리

### 2-1 타입스크립트 프로젝트 만들기

#### 패키지 설치

- `npm init --y` : package.json 설치
- `npm i -D typescrit ts-node` : 프로젝트를 실행할 때 필요한 페키지로 저장
- `npm i -D @types/node` : 타입, 라이브러리, 함수 검증하기 위해

#### tsconfig.json 파일 만들기

- `tec --init` : 필요한 옵션만 설정해서 간략하게 합니다.

```ts
// tsconfig.json
{
    "compilerOptions": {
        "module":"commonjs",
        "esModuleInterop":true,
        "target":"es5",
        "moduleResolution":"node",
        "outDir":"dist",
        "baseUrl":".",
        "sourceMap":true,
        "downleveIteration": false,
        "noImplicitAny":false,
        "paths": {"*" : ["node_modules/*"]},
    },
    "include":["src/**/*"]
}
```

#### src 디렉터리와 소스 파일 만들기

- `"include":["src/**/*"]`의 의미는 ./src와 ./src/utils 디렉터리에 이 프로젝터의 모든 타입스크립트 소스 파일이 있다는 뜻.
- `src/utils` 폴더 `src/index.ts`, `src/utils/makePerson.ts` 생성

```ts
// makePerson.ts
export function makePerson(name: string, age: number) {
  return { name: name, age: age };
}

export function testMakePerson() {
  console.log(makePerson("Jane", 22), makePerson("Jack", 33));
}
```

```ts
// index.ts
import { testMakePerson } from "./utils/makePerson";
testMakePerson();
```

- 터미널에 ts-node ./src/index.ts 혹은 ts-node ./src 실행
  `{ name: 'Jane', age: 22 } { name: 'Jack', age: 33 }`

#### package.json 수정

- 개발은 ts-node, 개발이 완료되면 타입스크립트 소스코드를 ES5 코드로 변환해 node로 실행해야 합니다.
- package.json의 `scrpits`에 `dev`, `build` 명령 추가
  `dev` : 개발 중에 src 디렉터리에 있는 index.ts 파일을 실행시키는 용도
  `build` : 개발 완료 후 프로그램을 배포하기 위해 dist 디렉터리에 ES5 JS 파일 만들때 사용
  `npm run *` 형태로 사용합니다.

```ts
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "ts-node src",
    "build": "tsc && node dist"
  },
```

### 2-2 모듈 이해하기

index.ts 같은 소스코드를 모듈이라고 합니다. 코드 관리와 유지, 보수를 편리하게 하기 위해 모듈마다 고유한 기능을 구현하는 방식으로 소스코드를 분할합니다.

#### export 키워드

- 다른 소스파일에서 자신의 기능을 제공하기 위해 사용하는 키워드. function, interface, class, type, let, const 키워드 앞에 사용 가능합니다.

#### import 키워드

- 다른 모듈의 기능을 이용하는 쪽에서 선언하는 키워드.
  export 키워드로 내보낸 심벌을 받아서 사용합니다.
- 기본 형태
  `import {심벌 목록} form '파일의 상대경로'`

#### import \* as 구문

- 기본 형태
  `import * as 심벌 from '파일 상대 경로'`
  export 파일 경로에 있는 모든 심벌을 하나로 심벌로 표현 가능.
  `import * as U from '파일 상대 경로'`
  `U.getNum(), U.setNum()..`

#### export default 구문

- 한 모듈이 내보내는 기능 중 오직 한 개에만 붙일 수 있습니다.
  import 할 때 {} 없이 사용 가능합니다.

#### 외부 패키지 사용할 때 import 문

- chance, ramda 패키지 설치
  `npm i -S chance ramda`
  `npm i -D @types/chance @types/ramda`
- imporit

```ts
import Chance from 'chance'
import * as from 'ramda'
```

### 2-3 tsconfig.json 파일 해석

- tsconfig.josn의 `"compilerOptions"`는 클론: 을 기준으로 `key : key value` 형태로 작성합니다.

  1. "module" : "commonjs"
     브라우저 아닌 환경에서도 CommonJS 방식으로 동작.
  2. "moduleResolution" : "node"
     Common JS는 노드에서 동작, node로 설정.
  3. "target" : "es5"
     대상 자바스크립트 버전 설정.
  4. "baseUrl" : "."
     현재 디렉터리를 의미하는 "."에 트랜스파일된 ES5 JS 파일을 저장.
  5. "outDir": "dist"
     baseUrl 설정값 기준 하위 디렉토리의 이름. dist 폴더에 빌드된 결과 저장.
  6. "paths" : "{ "\*": ["node_modules/_"]}"
     import from의 경로 설정. 외부 패키지를 위해 node_modules 설정.
  7. "esModuleInterop" : true
  8. "sourceMap" : true
     빌드 시 map 파일 생성. 디버깅 용도.
  9. "downlevelIteration" : true
     generator (생성기) 구문 위해 설정.
  10. "nolmplicitAny" : false
      타입을 명시하지 않으면 암시적으로 any 타입 생성. 이 오류 무시 설정.
