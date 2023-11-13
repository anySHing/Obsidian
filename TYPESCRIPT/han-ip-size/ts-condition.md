# 조건부 타입

조건부 타입은 `extends`와 삼항 연산자를 이용해 조건에 따라 각각 다른 타입을 정의하도록 돕는 문법이다.

```ts
type A = number extends string ? number : string; // string
```

조건부 타입은 위 코드처럼 `number extends string ?` 과 같은 조건식이 있고 이 조건이 참이라면 `?` 우측의 타입인 `Number` 타입이 결과가 되고 아니라며 `:` 우측의 타입인 `String` 타입이 결과가 된다.

위 조건부 타입의 조건식 `number extendss string`은 number 타입이 string 타입의 서브타입이 아니기 때문에 거짓이 되고 그 결과 타입 A는 string이 된다.<br>

이번엔 조건식에 객체 타입을 사용해보자

```ts
type ObjA = {
  a: number,
}

type ObjB = {
  a: number,
  b: number,
}

type B = objB extends ObjA ? number : string;
```

B는 `ObjB`가 `ObjA`의 서브 타입(자식)이므로 조건식이 참이 되어 `number` 타입이 된다.<br>

<h3>제네릭 조건부 타입</h3>

조건부 타입은 제네릭과 함께 사용할 때 그 위력이 극대화된다.

다음은 타입 변수에 `Number` 타입이 할당되면 `String` 타입을 반환하고 그렇지 않다면 `Number` 타입을 반환하는 조건부 타입이다.

```ts
type StringNumberSwitch<T> = T extends number ? string : number;

let varA: StringNumberSwitch<number>; // string

let varB: StringNumberSwitch<string>; // number
```

- `varA`는 `T`에 `number` 타입을 할당한다. 그 결과 조건식이 참이 되어 `string` 타입이 된다
- `varB`는 `T`에 `string` 타입을 할당한다. 그 결과 조건식이 거짓이 되어 `number` 타입이 된다.

<br>

실용적인 예제) 파라미터로 `String` 타입의 값을 제공받아 공백을 제거한 다음 반환하는 함수이다.

```ts
function removeSpaces(text: string) {
  return text.replaceAll(' ', '');
}

let result = removeSpaces('hi i\'m, PSH');
```

이 때 `removeSpaces()` 함수의 파라미터에 `undefined`나 `null` 타입의 값들도 제공될 수 있다고 가정한다. 그럼 파라미터의 타입을 다음과 같이 수정해야 한다.

```ts
function removeSpaces(text:string | undefined | null) {
  return text.replaceAll(' ', ''); // Error, text가 string이 아닐 수 있기 때문
}

let result = removeSpaces(''); // 이러면 오류 발생하겠죠
```

내가 아는 지식으로는 이럴 경우에 `Narrowing` 사용할 수 있다

```ts
function removeSpaces(text: string | null | undefined) {
  if (typeof text === 'string') {
    return text.replaceAll(' ', '');
  } else {
    return undefined;
  }
}
```

문제가 해결된 것 같지만 전혀 아니였고요 `result` 타입이 `string | undefined`로 추론되고요 개빡치고요

이럴 때 조건부 타입을 이용해 인수로 전달된 값의 타입이 `String`이면 반환값 타입도 `String`이고 아닐 경우엔 `undefined`로 만들어주면 된다.

```ts
// 아니 근데 이래도 에러가 발생하잖아
function removeSpaces<T>(text: T): T extends string ? string : undefined {
  if(typeof text === 'string') {
    return text.replaceAll(' ', ''); // Error
  } else {
    return undefined; // Error
  }
}

let result = removeSpaces('hi i\'m winterlood'); // string

let result2 = removeSpaces(undefind); // undefined
```

타입 변수 `T`를 추가하고 파라미터의 타입을 `T`로 정의한 다음 리턴 값의 타입을 `T extends string ? string : undefined`로 수정한다

이제 변수 `result`처럼 인수로 `String` 타입의 값을 전달하면 조건부 타입에 따라 리턴 값의 타입이 `String`이 된다. 또 `result2`처럼 인수로 `undefined`를 전달하면 리턴 값의 타입이 `undefined`가 된다.

근데 에러가 난다 에러가

**이유**: 조건부 타입의 결과를 함수 내부에서 알 수가 없다.

따라서 다음과 같이 타입 단언을 이용해 리턴 값의 타입을 `any` 타입으로 단언한다.

> 아니 any 쓰지 말라면서요 이사람들아

```ts
function removeSpaces<T>(text: T): T extends string ? string : undefined {
  if (typeof text === 'string') {
    return text.replaceAll(' ', '') as any;
  } else {
    return undefined as any;
  }
}

(...)
```

any를 보고 어이가 없는 당신을 위해 해결책이 마련되었다. 함수 오버로딩을 쓰자!

```ts
function removeSpaces<T>(text: T): T extends string ? string : undefined;
function removeSpaces(text: any) {
  if (typeof text === 'string') {
    return text.replaceAll(' ', '');
  } else {
    return undefined;
  }
}

(...)
```

나는 근데 `text:any`도 거슬린다.

## 분산적인 조건부 타입

```ts
type StringNumberwitch<T> = T extends number ? string : number;

let a: StringNumberSwitch<number>;

let b: StringNumberSwitch<string>;
```

변수 a의 타입은 조건식이 참이 되어 `string`으로 정의되고,

변수 b의 타입은 조선식이 거짓이 되어 `number`로 정의된다.<br>

그럼 타입 변수에 `Union`을 할당하면 어떻게 될까?

```ts
type StringNumberSwitch<T> = T extends number ? stirng : number;

(...)

let c: StringNumberSwitch<number | string>; // string | number
```

지금까지 배운 조건부 타입 문법에 따라 변수 `c`의 타입은 `number | string`은 `number`의 자식(서브 타입)이 아니므로 조건식이 거짓이 되어 `number`가 될거라고 예상할 수 있다.

근데 효자스크립트가 나를 배신하고 `string | number`로 추론때렸다...

**이유**: 조건부 타입의 타입 변수에 `Union` 타입을 할당하면 *분산적인 조건부 타입*으로 조건부 타입이 업그레이드 되기 때문

**분산적인 조건부 타입**은 다음과 같이 동작한다.

1. 타입 변수에 할당한 `Union` 타입 내부의 모든 타입이 분리된다.
2. 따라서 `StringNumberSwitch<number | string>` 타입은 다음과 같이 분산된다.
   * `StringNumberSwitch<number>`
   * `StringNumberSwitch<string>`
3. 그리고 다음으로 분산된 각 타입의 결과를 모아 다시 `Union` 타입으로 묶는다.
4. 결과: `number | string`

<br>

<h3>Exclude 조건부 타입 구현하기</h3>

분산적인 조건부 타입의 특징을 이용하면 매우 다양한 타입을 정의할 수 있다.

예를 들자면 `Union` 타입으로부터 특정 타입만 제거하는 Exclude 타입을 다음과 같이 정의할 수 있다.

```ts
type Exclude<T, U> = T extends U ? never : T;

type A = Exclude<number | string | boolean, string>;
```

위 코드는 다음의 흐름으로 동작한다.

1. `Union` 타입이 분리된다.
   * Exclude<number, string> 
   * Exclude<string, string> 
   * Exclude<boolean, string>
2. 각 분리된 타입을 모두 계산한다
   * `T = number, U = string`일 때 `number extends string`은 거짓이므로 결과는 `number`
   * `T = string, U = string`일 때 `string extends string`은 참이므로 결과는 `never`
   * `T = boolean, U = string`일 때 `boolean extends string`은 거짓이므로 결과는 `boolean`
3. 계산된 타입들을 모두 `Union`으로 묶는다.
   * 결과: `number | never | boolean`

<br>

계산 결과 타입 A는 `number | never | boolean` 타입으로 정의된다. 그런데 여기서 공집합을 의미하는 `never` 타입은 `Union`으로 묶일 경우 그냥 사라진다.

**이유**: 공집합과 어떤 집합 A의 합집합은 A이기 때문

따라서 최종적으로 타입 A는 `number | boolean` 타입이 된다.

<br>

## infer

`infer`는 조건부 타입 내에서 특정 타입을 추론하는 문법이다.

다음과 같이 특정 함수 타입에서 리턴 값의 타입만 추출하는 특수한 조건부 타입인 `ReturnType`을 만들 때 이용할 수 있다.

```ts
type ReturnType<T> = T extends () => infer R ? R : never;

type FuncA = () => string;

type FuncB = () => number;

type A = ReturnType<FuncA>; // string

type B = ReturnType<FuncB>; // number
```

조건식 `T extends () => infer R`에서 `infer R`은 이 조건식이 참이 되도록 만들 수 있는 최적의 `R` 타입을 추론하라는 의미

따라서 A 타입을 계산할 때의 위 코드의 흐름은 다음과 같다.

1. 타입 변수 T에 함수 타입 FuncA가 할당된다.
2. T는 `() => string`이 된다.
3. 조건부 타입의 조건식은 다음 형태가 된다. `() => string extends () => infer R ? R : never`
4. 조건식을 참으로 만드는 `R` 타입을 추론한다. 그 결과 `R`은 `string`
5. 추론이 가능하면 이 조건식을 참으로 판단한다.

만약 다음과 같이 추론이 불가능하다면 조건식을 거짓으로 판단한다.

```ts
type ReturnType<T> = T extends () => infer R ? R : never;

type FuncA = () => string;

type FuncB = () => number;

type A = ReturnType<FuncA>; // string

type B = ReturnType<FuncB>; // number

type C = ReturnType<number>; // 조건식을 만족하는 R 추론 불가 -> never
```

<br>

다음은 Promise의 resolve 타입을 infer를 이용해 추출하는 예

```ts
type PromiseUnpack<T> = T extends Promise<infer R> ? R : never;
// 1. T는 프로미스 타입이어야 한다.
// 2. 프로미스 타입의 리턴 값 타입을 반환해야 한다.

type PromiseA = PromiseUnpack<Promise<number>>; // number

type PromiseB = PromiseUnpack<Promise<string>>; // string
```