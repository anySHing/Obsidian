# 함수 타입

## 함수의 타입을 정의하는 방법

함수의 타입은 다음과 같이 매개 변수와 반환 값의 타입으로 결정된다.

```ts
function func(a: number, b: number): number {
  return a + b;
}
```

함수의 반환 값 타입은 자동으로 추론되기 때문에 생략해도 됨

```ts
function func(a: number, b: number) {
  return a + b;
}
```

<br>

<h3>화살표 함수 타입 정의하기</h3>

```ts
const add = (a: number, b: number): number => a + b;
```

화살표 함수 또한 리턴 값 타입 생략 가능

```ts
const add = (a: number, b: number) => a + b
```

<br>

<h3>매개 변수 기본 값 설정하기</h3>

함수의 매개 변수에 기본 값이 설정되어 있으면 타입이 자동으로 추론된다.

```ts
function introduce(name = '박성현') {
  console.log(`name: ${name}`);
}
```

<br>

<h3>선택적 매개 변수 설정하기</h3>

매개 변수의 이름 뒤에 물음표(`?`)를 붙여주면 선택적 매개 변수가 되어 생략 가능

```ts
function introduce(name = '성현', height?: number) {
  console.log(`name: ${name}`);
  console.log(`height: ${height}`);
}

introduce('홍길동',188);
introduce('고길동',168);
```

위 코드의 `height`같은 선택적 매개 변수의 타입은 자동으로 `undefined`와 유니온 된 타입으로 추론된다.

-> 타입 가드를 써야할 것

```ts
function introduce(name = '성현', height?: number) {
  console.log(`name: ${name}`);
  if(typeof height === 'number') {
    console.log(`height: ${height}`);
  }
}
```

**주의할 점**: 선택적 매개 변수는 필수 매개 변수 앞에 올 수 없다.

<br>

<h3>나머지 매개 변수</h3>

JS의 rest 파라미터 관련 내용이다.

다음과 같이 여러 개의 숫자를 인수로 받는 함수가 있다고 가정

```JS
function getSum(...numbers) {
  let sum = 0;
  numbers.forEach((number) => (sum += number));
  return sum;
};
```

`getSum()`은 나머지 매개 변수 `numbers`로 배열 형태로 `number` 타입의 인수들을 담은 배열을 전달받는다.

<br>

이 때 `numbers` 파라미터의 타입은 다음과 같이 정의하면 된다.

```ts
function getSum(...numbers: number[]) {
  (...)
}
```

<br>

이 때 만약 나머지 매개 변수들의 길이를 고정하고 싶다면 튜플을 사용할 수 있다.

```ts
function getSum(...numbers: [number, number, number]) {
  (...)
}

getSum(1, 2, 3) // OK
getSum(1, 2, 3, 4, 5) // Error
```

<br>

## 함수 타입 표현식과 호출 시그니쳐

### 함수 타입 표현식 (Function Type Expression)

함수 타입을 타입 별칭과 함께 별도로 정의하는 것

```ts
type Add = (a: number, b: number) => number;

const add: Add = (a, b) => a + b;
```

다음과 같이 여러 개의 함수가 동일한 타입을 갖는 경우에 요긴하게 사용된다.

```ts
const add = (a: number, b: number) => a + b;
const sub = (a: number, b: number) => a - b;
const multiply = (a: number, b: number) => a * b;
const divide = (a: number, b: number) => a / b;
```

<br>

```ts
type Operation = (a: number, b: number) => number;
const add: Operation = (a, b) => a + b;
const sub: Operation = (a, b) => a - b;
const multiply: Operation = (a, b) => a * b;
const divide: Operation = (a, b) => a / b;
```

<br>

꼭 타입 별칭과 함께 사용되어야 하는 것은 아니다. 그냥 함수 타입 표현식을 타입 주석에 사용해도 문제는 없음
```ts
const add: (a: number, b: number) => number = (a, b) => a + b;
```

<br>

## 호출 시그니쳐

함수 타입 표현식과 동일하게 함수의 타입을 별도로 정의하는 방식

```ts
type Operation2 = {
  (a: number, b: number): number;
}

const add2: Operation2 = (a, b) => a + b;
const sub2: Operation2 = (a, b) => a - b;
const multiply2: Operation2 = (a, b) => a * b;
const divide2: Operation2 = (a, b) => a / b;
```

JS에서는 함수도 객체이기 때문에 객체를 정의하듯 함수의 타입을 별도로 정의할 수 있다.

<br>

참고로 호출 시그니쳐 아래에 프로퍼티를 추가 정의하는 것도 가능하다.

이렇게 할 경우 함수이자 일반 객체를 의미하는 타입으로 정의되며, 하이브리드 타입이라고 부름

```ts
type Operation2 = {
  (a: number, b: number): number,
  name: string,
}

const add2: Operation2 = (a, b) => a + b;
(...)

add2(1, 2);
add.name;
```

근데 이럴거면 클래스를 쓰지

<br>

# 함수 타입의 호환성

## 함수 타입의 호환성이란 ?

특정 함수 타입을 다른 함수 타입으로 괜찮은지 판단하는 것

다음 2가지 기준으로 함수 타입의 호환성을 판단

* 두 함수의 리턴 타입이 호환되는지
* 두 함수의 파라미터의 타입이 호환되는지

### 기준 1: 리턴 타입이 호환되는가 ?

A와 B 함수 타입이 있다고 가정할 때 A 리턴 타입이 B 리턴 타입의 슈퍼 타입이라면 두 타입은 호환된다.

```ts
type A = () => number;
type B = () => 10;

let a: A = () => 10;
let b: B = () => 10;

a = b; // OK
b = a; // Error
```
A의 리턴 타입은 `Number`, B는 `10 Number Literal`이므로 a에 b를 할당하는 것은 가능하나 반대는 불가능하다.

<br>

### 기준 2: 파라미터의 타입이 호환되는가 ?

파라미터 타입이 호환되는지 판단할 때에는 파라미터 개수가 같은지 다른지에 따라 두 가지 유형으로 나뉘게 된다.

<h3>2-1. 파라미터의 개수가 같을 때</h3>

두 함수 타입 C와 D가 있다고 가정할 때 두 타입의 파라미터 개수가 같다면 C 파라미터 타입이 D 타라미터 타입의 서브 타입일 때에 호환된다.

```ts
type C = (value: number) => void;
type D = (value: 10) => void;

let c: C = (value) => {};
let d: D = (value) => {};

c = d; // Error
d = c; // OK
```

이렇게 보니까 요상한데 객체로 예를 들어보면 이해가 잘 됨

<br>

```ts
type Animal = {
  name: string,
};

type Dog = {
  name: string,
  color: string,
};

let animalFunc = (animal: Animal) => {
  console.log(animal.name);
};

let dogFunc = (dog: Dog) => {
  console.log(dog.name);
  console.log(dog.color);
};

animalFunc = dogFunc; // Error;
dogFunc = animalFunc; // OK
```

`animalFunc()`에 `dogFunc()`를 할당하는 것은 불가능하다. 

이유? `AnimalFunc()`에는 `color` 프로퍼티에 관한 정보가 없기 때문

<br>

<h3>2-2. 파라미터의 개수가 다를 때</h3>

간단

```ts
type Func1 = (a: number, b: number) => void;
type Func2 = (a: number) => void;

let func1: Func1 = (a, b) => {};
let func2: Func2 = (a) => {};

func1 = func2; // OK
func2 = func1; // Error
```

당연히 `func2()` 에는 b에 관한 정보가 없으니까 !

<br>

# 함수 오버로딩

하나의 함수를 파라미터의 개수나 타입에 따라 다르게 동작하도록 만드는 문법

TS에서 함수 오버로딩을 구현하려면 버전별 오버로드 시그니쳐를 만들어야 함 ⬇️

```ts
function func(a: number): void;
function func(a: number, b: number, c: number): void;
```

이렇게 구현부 없이 선언부만 만들어둔 함수를 `오버로드 시그니쳐`라고 함.

위에서는 2개의 오버로드 시그니쳐를 만들었으며 각각 함수의 버전을 의미. func 함수는 파라미터를 1개 받는 버전과 3개 받는 버전이 있다고 알리는 것과 같음

<br>

오버로드 시그니쳐를 만들었다면 다음으로는 `구현 시그니쳐`를 만들어야 한다.

```ts
// 오버로드 시그니쳐
function func(a: number): void;
function func(a: number, b:number, c: number): void;

// 구현 시그니쳐
function func(a: number, b?: number, c?: number) {
  if(typeof b === 'number' && typeof c === 'number) {
    consol.elog(a + b + c);
  } else {
    console.log(a * 20);
  }
}

func(1); // OK
func(1, 2); // Error
func(1, 2, 3); // OK
```

<br>

# 사용자 정의 타입 가드

`사용자 정의 타입 가드`: 참 또는 거짓을 반환하는 함수를 이용해 우리 입맛대로 타입 가드를 만들 수 있도록 도와주는 TS의 문법

어떻게 사용하는 것인가

```ts
type Dog = {
  name: string,
  isBark: boolean,
};

type Cat = {
  name: string,
  isScratch: boolean,
};

type Animal = Dog | Cat;

function warning(animal: Animal) {
  if ('isBark' in animal) {
    console.log(animal.isBark ? '짖습니다' : '안짖어요');
  } else if ('isScratch' in animal) {
    console.log(animal.isScratch ? '할큅니다' : '안할큅니다');
  }
}
```

2개의 타입 `Dog`와 `Cat`을 정의하고 두 타입의 유니온 타입인 `Animal` 타입을 정의한다.

다음으로 매개 변수로 `Animal` 타입의 값을 받아 동물에 따라 각각 다른 경고를 출력하는 함수 `warning()`을 만들어주었다.

만약 이 함수를 호출하고 `Dog`를 Arg로 전달한다면 '짖습니다' 또는 '안짖어요'를 출력할 것이고, `Cat`을 전달한다면 '할큅니다' 또는 '안할큅니다'를 전달할 것

<br>

그런데 이렇게 `in` 연산자를 사용하여 타입을 좁히는 것은 별로 좋지 않음

`Dog`의 프로퍼티가 변경되거나 하면 타입 가드가 제대로 동작하지 않을 수 있다.

```ts
type Dog = {
  name: stirng,
  isBarked: boolean, // isBark 였음
}
```

따라서 이럴 때는 다음과 같이 함수를 이용해 커스텀 타입 가드를 만들어 타입을 좁히는게 더 낫다.

```ts
(...)

// Dog 타입인지 확인하는 타입 가드
function isDog(animal: Animal): animal is Dog {
  return (animal as Dog).isBark !== undefined;
}

// Cat 타입인지 확인하는 타입 가드
function isCat(animal: Animal): animal is Cat {
  return (animal as Cat).isScratch !== undefined;
}

function warning(animal: Animal) {
  if (isDog(animal)) {
    console.log(animal.isBark ? '짖습니다' : '안짖어요');
  } else {
    console.log(animal.isScratch ? '할큅니다' : '안할퀴어요');
  }
}
```

`isDog()` 함수는 파라미터로 받은 값이 `Dog` 타입이라면 `true`, 아니라면 `false`를 리턴한다. 이 때 리턴 값의 타입으로 `animal is Dog`를 정의하면 이 함수가 true를 반환하면 조건문 내부에서는 이 값이 `Dog` 타입입을 보장한다는 의미가 됨.

따라서 `warning()` 함수에서 `isDog()` 함수를 호출해 매개 변수의 값이 `Dog`인지 확인하고 타입을 좁힐 수 있다.