# 타입스크립트 개론

<br><hr>

## 4. 타입스크립트 컴파일 옵션 설정하기

### 컴파일러 옵션이란

TS의 컴파일은 우리가 작성한 코드에 타입 오류가 없는지 검사하고 오류가 없다면 JS로 변환하는 것이다.

이 과정에서 아주 세부정인 사항들, 예를 들자면 얼마나 엄격하게 타입을 검사할건지, 컴파일 시 생성되는 JS 코드의 버전은 어떻게 할 것인지 등등을 컴파일러 옵션에서 설정할 수 있다.

<br>

### 컴파일러 옵션 자동 생성하기

vsc 터미널에 `tsc --init` 이라고 입력하면 `tsconfig.json` 파일을 생성해준다. 여기서 주석만 해제 하는 식으로도 사용할 수 있지만 직접 설정해볼 것임

<br>

### 컴파일러 옵션 직접 설정하기

<h3>include</h3>

tsc에게 컴파일 할 타입스크립트 파일의 범위와 위치를 알려주는 옵션

```ts
{
  'include': ['src'],
}
```

이렇게 입력할 경우 현재 프로젝트 폴더의 `/src` 폴더 내 모든 *.ts* 파일이 동시에 컴파일된다. 웹팩의 entry 같은 것 인듯..

<br>

<h3>target</h3>

컴파일 결과로 생성되는 JS 코드의 버전을 설정하는 옵션

```ts
{
  'compilerOptions': {
    'target': 'ES5'
  },
  'include': ['src'],
}
```

이렇게 입력해두면 TS 코드가 컴파일 시 ES5 버전이 JS 코드로 변환된다.

<br>

<h3>module</h3>

컴파일 시 뱉어주는 JS 코드의 모듈 시스템을 설정하는 옵션

```ts
{
  'compilerOptions': {
    'target': 'ESNext',
    'module': 'CommonJS'
  },
  'include': ['src'],
}
```

`CommonJS`를 사용할 경우 `require`, `exports` 등의 구문을 사용하고,

`ESNext`를 사용할 경우 `import`, `export` 등의 구문을 사용하여 변환함

<br>

<h3>outDir</h3>

TS 컴파일 결과로 생성할 JS 파일의 위치를 결정하는 옵션

```ts
{
  'compilerOptions': {
    'target': 'ESNext',
    'module': 'ESNext',
    'outDir': 'dist',
  },
  'include': ['src'],
}
```

컴파일 결과가 `dist` 폴더에 생성된다. 웹팩의 `output` 같은 역할인가봄

<br>

<h3>strict</h3>

TS 컴파일러의 타입 검사 엄격함 정도를 결정하는 옵션

```ts
export const hello = (message) => {
  console.log(`hello, ${message}`);
}
```

이렇게 작성된 코드에 `tsconfig.js`에서 `strict:true`를 걸어줄 경우

`message` 파라미터가 `any` 타입이라면서 화를 낸다.

<br>

<h3>ModuleDetection</h3>

TS의 모든 파일은 기본적으로 전역 파일(모듈)로 취급된다.

```ts
// a.ts
const a = 1;

// b.ts
const a = 1;
```

이런 식으로 작성하게 되면 오류가 발생한다.

<br>

이럴 때에 각 파일에 모듈 시스템 키워드(export, import)를 최소 하나 이상 사용해 해당 파일을 전역 모듈이 아닌 로컬(독립) 모듈로 취급되도록 만들어야 하는데 이를 **자동화** 하는 옵션

<br>

```ts
{
  'compilerOptions': {
    'target': 'ESNext',
    'module': 'ESNext',
    'outDir': 'dist',
    'moduleDetection': 'force'
  },
  'include': ['src'],
}
```

이런 식으로 작성해주면 모든 TS 파일이 로컬(독립) 모듈로 취급된다.

<br>

<h3>ts-node</h3>

`moduleDetection` 옵션을 활성화하고 TS 파일에서 모듈 시스템을 사용하게 되면 ts-node로 실행 시 오류가 발생한다.

이 때 ts-node 옵션을 설정하면 ts-node로 타입스크립트 모듈을 실행할 수 있게 된다.

```ts
{
  "compilerOptions": {
    "target": "ESNext",
    "module": "ESNext",
    "outDir": "dist",
		"moduleDetection": "force"
  },
	"ts-node": {
		"esm": true
	},
  "include": ["src"]
}
```

[참고 링크](https://www.typescriptlang.org/tsconfig)