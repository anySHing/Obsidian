# 제네릭

함수나 인터페이스, 타입 별칭, 클래스 등을 다양한 타입과 함께 동작하도록 만들어주는 기능

<h3>제네릭이 필요한 상황</h3>

다음과 같이 다양한 타입의 매개 변수를 받고, 해당 매개 변수를 그대로 반환하는 함수가 하나 필요하다고 가정

```ts
function func(value: any) {
  return value;
}

let num = func(10); // any

let str =func('string') // any
```

다양한 타입의 매개 변수를 받아야 하기 때문에 value의 타입을 일단 any로 해두었다. (unknown도 가능하다.)

<br>

이 함수는 인수로 전달한 값을 그냥 그대로 반환하는 단순한 함수이다. 따라서 변수 num에는 10이 저장되고 변수 str에는 'string'이 저장된다. 그런데 현재 num과 str의 타입은 any 타입이 된다.

func 함수의 반환 값 타입이 return 문을 기준으로 추론되었기 때문.

이렇게 함수 호출 결과를 저장하는 num, str 등의 변수가 any 타입으로 추론되면 다음과 같은 문제점이 발생한다.

```ts
function func(value: any) {
  return value;
}

let num = func(10);
let str = func('string');

num.toUpperCase(); // 에러가 떠야 하는데 ?
```

매개 변수의 타입을 any 말고 unknown 으로 했을 경우에는

반대로 number 타입인데도 `toFixed()` 같은 메서드 호출을 오류로 취급해버리게 된다.

<br>

지금까지 배운대로만 타입을 좁히려면 이런 식의 비효율적인 코드가 나온다.

```ts
function func(value: unknown) {
  return value;
}

let num = func(10); // unknown
let str = func('string'); // unknown

if(typeof num === 'number') {
  num.toFixed();
}
```

이럴 때 제네릭을 사용하면 문제를 간단히 해결할 수 있다.

<br>

<h3>제네릭(Generic) 함수</h3>

두루두루 모든 타입의 값을 다 적용할 수 있는 그런 범용적인 함수 정도로 이해할 수 있다.

```ts
function func<T>(value: T): T {
  return value;
};

let num = func(10); // number
```

`T`에 어떤 타입이 할당될 지는 함수가 호출될 때 결정된다. `func(10)`처럼 Number 타입의 값을 인수로 전달하면 매개 변수 value에 Number 타입의 값이 저장되면서 T가 Number 타입으로 추론된다.

<br>

제네릭 함수를 호출할 때 다음과 같이 타입 변수에 할당할 타입을 직접 명시하는 것도 가능하다.

```ts
function func<T>(value: T): T {
  return value;
}

let arr = func<[number, number, number]>([1, 2, 3]);
```

위 코드의 흐름은 다음과 같다.

1. T에 `[Number, Number, Number]` 튜플 타입이 할당됨
2. 매개 변수 value와 리턴 값 타입이 모두 튜플 타입이 됨

<br>

만약 위 코드에서 타입 변수에 할당할 타입을 튜플 타입으로 설정하지 않았다면 T가 `number[]` 타입으로 추론되었을 것. TS는 타입을 추론할 때 항상 일반적이고 좀 더 범용적인 타입으로 추론하기 때문

타입 변수에 할당하고 싶은 특정 타입이 존재한다면 이런 식으로 꺾쇠를 열고 타입을 직접 작성해주는 것이 좋다. 대부분은 굳이 작성하지 않아도 되긴 함

<br>

## 타입 변수 응용하기

<h3>사례 1</h3>

만약 2개의 타입 변수가 필요한 상황이라면 다음과 같이 T, U 처럼 2개의 타입 변수를 사용해도 된다.

```ts
function swap<T, U>(a: T, b: U) {
  return [b, a];
}

const [a, b] = swap('1', 2);
```

위 코드에서 `T`는 `String`, `U`는 `Number` 타입으로 추론된다.

<br>

<h3>사례 2</h3>

다양한 배열 타입을 인수로 받는 제네릭 함수를 만들어야 한다면 다음과 같이 할 수 있다.

```ts
function returnFirstValue<T>(data: T[]) {
  return data[0];
}

let num = returnFirstValue([0, 1, 2]); // number
let str = returnFirstValue([1, 'hello', 'mynameis']); // number | string
```

함수 매개 변수 data의 타입을 `T[]`로 설정했기 때문에 배열이 아닌 값은 인수로 전달할 수 없다. 

배열을 진수로 전달하면 `T`는 배열의 요소 타입으로 할당된다.

<br>

첫번째 호출에서는 인수로 `Number[]` 타입의 값을 전달했으므로 이 때의 `T`는 `Number` 타입으로 추론된다. 이 때의 함수 반환 값 타입은 `Number` 타입이 된다.

두번째 호출에서는 인수로 `(String | Number)[]` 타입의 값을 전달했으므로 이 때의 `T`는 `String | Number` 타입으로 추론된다. 이 떄의 함수 반환 값 타입은 `String | Number` 타입이 된다.

<br>

<h3>사례 3</h3>

위 사례에서 만약 반환 값의 타입을 배열의 첫번쨰 요소의 타입이 되도록 하려면 다음과 같이 튜플 타입과 나머지 파라미터를 이용하면 된다.

```ts
function returnFirstValue<T>(data: [T, ...unknown[]]) {
  return data[0];
}

let str = returnFirstValue([1, 'hello', 'mynameis']); // number
```

함수 매개 변수의 타입을 정의할 때 튜플 타입을 이용해 첫번째 요소의 타입은 `T` 그리고 나머지 요소의 타입은 `...unknown[]`으로 길이도 타입도 상관없도록 정의한다.

함수를 호출하고 `[1, 'hello', 'mynameis']` 같은 배열 타입의 값을 인수로 전달하면 T는 첫번째 요소의 타입인 `Number` 타입이 된다. 따라서 함수 반환 값 또한 `Number` 타입이 된다.

<br>

<h3>사례 4</h3>

마지막으로 살펴볼 사례는 타입 변수를 제한하는 케이스이다.

타입 변수를 제한한다는 것은 함수를 호출하고 인수로 전달할 수 있는 값의 범위에 제한을 두는 것을 의미<br>

다음은 타입 변수를 적어도 length 프로퍼티를 갖는 객체 타입으로 제한하는 예시

```ts
function getLength<T extends {length: number}>(data: T) {
  return data.length;
}

getLength('123'); // OK

getLength([1, 2, 3]); // OK

getLength({length: 1}); // OK

getLength(undefined); // Error;

getLength(null); // Error;
```

타입 변수를 제한할 때는 확장(extends)을 이용한다.<br>

위와 같이 `T extends { length: number }`라고 정의하면 T는 이제 `{ length: number }` 객체 타입의 서브 타입이 된다.

바꿔말하면 이제 T는 무조건 Number 타입의 프로퍼티 length를 가지고 있는 타입이 되어야 한다는 것.

<br>

## Map 메서드 타입 정의하기

JS의 배열 메서드 `Map()`은 다음과 같이 원본 배열의 각 요소에 콜백 함수를 수행하고 반환된 값들을 모아 새로운 배열로 만들어 반환한다.

```js
const arr = [1, 2, 3];
const newArr = arr.map(it => it * 2); // [2, 4, 6]
```

map 메서드를 직접 함수로 만들고 타입도 정의한다. 먼저 제네릭 함수가 아닌 일반적인 함수로 만든다.

```ts
function map(arr: unknown[], callback: (item: unknown) => unknown): unknown[] {}
```

메서드를 적용할 배열을 매개 변수 arr로 받고, 콜백 함수를 매개 변수 callback으로 받는다.

`map()` 메서드는 모든 타입의 배열에 적용할 수 있기 때문에 타입은 `unknown[]`으로 정의한다. 

`callback`의 타입은 배열 요소 하나를 매개 변수로 받아 특정 값을 반환하는 함수로 정의한다.

`map()`메서드의 리턴 타입은 배열 타입으로 정의한다.<br>

다음으로는 이 함수에 타입 변수를 선언하여 제네릭 함수로 만든다.

```ts
function map<T>(arr: T[], callback: (item: T) => T): T[] {};
```

모든 `unknown` 타입을 타입 변수 T로 대체했다. 다음으로는 함수 내부를 구현할 차례

```ts
function map<T>(arr: T[], callback: (item: T) => T): T[] {
  let result = [];
  for (let i = 0; i < arr.length; i++) {
    result.push(callback(arr[i]));
  }
  return result;
}
```

1차적으로 map 함수를 만들고 타입 정의도 마쳤다. 이제 함수를 호출한다.

```ts
const arr = [1, 2, 3];

function map<T>(arr: T[], callback: (item: T) => T): T[] {
  (...)
}

map(arr, it => it * 2); // [2, 4, 6]. number[] 타입의 배열을 반환
```

매개 변수 `arr`에 `number[]` 타입의 배열을 제공하니 타입 변수 `T`가 `number`로 잘 추론되고 그 결과 `map()` 함수의 리턴 타입도 `number[]`가 되었다.<br>

근데 문제가 한 가지 있음. 함수 호출을 다음과 같이 수정하면 오류가 발생

```ts
const arr = [1, 2, 3];

function map<T>(arr: T[], callback: (item: T) => T): T[] {
  (...)
}

map(arr, it => it.toString()); // ❌
```

콜백 함수가 모든 배열 요소를 `String` 타입으로 변환하도록 수정했다. 이러면 오류가 발생함.

첫 번째 인수로 `arr`을 전달했을 때 변수 `T`에는 `number` 타입이 할당되었기 때문에 콜백 함수의 리턴 타입도 `number` 타입이 되어야 하기 때문<br>

그런데 `map()` 메서드는 이렇게 원본 배열 타입과 다른 타입의 배열로도 변환할 수 있어야 한다. 따라서 타입 변수를 하나 더 추가해 다음과 같이 수정해야 한다.

```ts
const arr = [1, 2, 3];

function map<T, U>(arr: T[], callback: (item: T) => U): U[] {
  (...)
}

map(arr, it => it.toString()); // ['1', '2', '3'], string[] 타입의 배열을 반환
```

원본 배열의 타입과 새롭게 반환하는 배열의 타입을 다르게 설정해주었더니 호출문의 오류가 사라졌다.

<br>

<h3>ForEach 메서드 타입 정의하기</h3>

`forEach()` 메서드는 다음과 같이 배열의 모든 요소에 콜백 함수를 한번씩 수행해주는 메서드이다.

```js
const arr = [1, 2, 3];

arr.forEach(it => console.log(it));
```

<br>

뽀 이치는 맵 보다 훨씬 만들기 쉽고 타입 정의도 간단하다.

```ts
function forEach<T>(arr: T[], callback: (item: T) => void) {
  for (let i = 0; i < arr.length; i++) {
    callback(arr[i]);
  }
}
```

`Map()`과 동일하게 2개의 매개 변수를 받는다.

첫 번째 매개 변수 `arr`에는 순회 대상 배열을 제공받고 두 번째 매개 변수 `callback`에는 모든 배열 요소에 수행할 함수를 제공받는다. 이 때 아까 `Map()` 메서드의 타입 정의와는 달리 `forEach()` 메서드는 리턴 값이 없는 메서드이므로 콜백 함수의 리턴 타입을 `void`로 정의한다.

<br>

## 제네릭 인터페이스, 제네릭 타입 별칭

### 제네릭 인터페이스

제네릭은 인터페이스에도 적용할 수 있다. 다음과 같이 인터페이스에 타입 변수를 선언해 사용하면 됨

```ts
interface KeyPair<K, V> {
  key: K,
  value: V,
}
```

키값쌍을 저장하는 객체의 타입을 제네릭 인터페이스로 정의했다.<br>

다음과 같이 변수의 타입으로 정의하여 사용할 수 있다.

```ts
let keyPair: KeyPair<string, number> = {
  key: 'key',
  value: 0,
};

let keyPair2: KeyPair<boolean, string[]> = {
  key: true,
  value: ['1'],
}
```

변수 `keyPair`의 타입으로 `KeyPair<string, number>`를 정의했다. 그 결과 `K`에는 `string`, `V`에는 `number` 타입이 각각 할당되어 `key` 프로퍼티는 `string` 타입이고, `value` 프로퍼티는 `number` 타입인 객체 타입이 된다. 따라서 값으로 해당 타입의 객체를 저장한다.<br>

변수 `keyPair2`의 타입으로 `KeyPair<boolean, string[]>`을 정의했다. 그 결과 `K`에는 `boolean`, `V`에는 `string[]` 타입이 각각 할당되어 `key` 프로퍼티는 `boolean` 타입이고, `value` 프로퍼티는 `string[]` 타입인 객체 타입이 된다. 따라서 값으로 해당 타입의 객체를 저장한다.<br>

**주의할 점**: 제네릭 인터페이스는 제네릭 함수와는 달리 변수의 타입으로 정의할 때 반드시 꺾쇠와 함께 타입 변수에 할당할 타입을 명시해주어야 한다.

**이유**: 제네릭 함수는 매개 변수에 제공되는 값의 타입을 기준으로 타입 변수의 타입을 추론할 수 있지만 인터페이스는 마땅히 추론할 수 있는 값이 없기 때문<br>

<h3>인덱스 시그니쳐와 함께 사용하기</h3>

제네릭 인터페이스는 인덱스 시그니쳐와 함께 사용하면 다음과 같이 기존보다 훨씬 더 유연한 객체 타입을 정의할 수 있다.

```ts
interface Map<V> {
  [key: string]: V;
}

let stringMap: Map<string> = {
  key: 'value',
};

let booleanMap: Map<boolean> = {
  key: true,
}
```

한 개의 타입 변수 `V`를 갖는 제네릭 인터페이스 `Map`을 정의했다. 이 인터페이스는 인덱스 시그니쳐로 `key`의 타입은 `string`, `value`의 타입은 `V`인 모든 객체 타입을 포함하는 타입이다.<br>

변수 `stringMap`의 타입을 `Map<string>`으로 정의했다. 따라서 `V`가 `string` 타입이 되어 이 변수의 타입은 `key`는 `string`, `value`는 `string`인 모든 프로퍼티를 포함하는 객체 타입으로 정의된다.

변수 `booleanMap`의 타입은 `Map<boolean>`으로 정의했다. 따라서 `V`가 `boolean` 타입이 되어 이 변수의 타입은 `key`는 `string`, `value`는 `boolean`인 모든 프로퍼티를 포함하는 객체 타입으로 정의된다.<br>

### 제네릭 타입 별칭

인터페이스와 마찬가지로 타입 별칭에도 역시 제네릭을 적용할 수 있다.

```ts
type Map2<V> = {
  [key: string]: V;
};

let stringMap2: Map2<string> = {
  key: 'string',
}
```

제네릭 타입 별칭을 사용할 때도 제네릭 인터페이스와 마찬가지로 타입으로 정의될 때 반드시 타입 변수에 설정할 타입을 명시해주어야 한다.<br>

<h3>제네릭 인터페이스의 활용 예</h3>

개발자 또는 학생이 이용하는 어떤 프로그램이 있다고 가정

```ts
interface Student {
  type: 'student',
  school: string,
};

interface Developer {
  type: 'developer',
  skill: string,
};

interface User {
  name: string,
  profile: Student | Developer
}

function goToSchool(user: User<Student>) {
  if(user.profile.type !== 'student') {
    console.log('잘 못 오셨습니다.');
    return;
  }

  const school = user.profile.school;
  console.log(`${school}로 등교 완료`);
}

const developerUser: User = {
  name: '박성현',
  profile: {
    type: 'developer',
    skill: 'typescript',
  },
};

const studentUser: User = {
  name: '홍길동',
  profile: {
    type: 'student',
    school: '배재학당',
  }
}
```

학생을 의미하는 `Student`와 개발자를 의미하는 `Developer` 타입을 정의.<br>

두 타입 모두 `String Literal` 타입의 `type` 프로퍼티를 갖고 있으며, 서로소 유니온 타입이다.

그리고 그 아래에 학생일 수도 개발자일 수도 있는 `User` 타입을 정의한다. 특정 객체가 학생이라면 `profile` 프로퍼티에 `Student` 타입의 객체가 저장될 것이고, 그렇지 않다면 `Developer` 타입의 객체가 저장될 것

그리고 `Student`만 사용할 수 있는 함수 `goToSchool()`을 선언했다. 이 함수에서는 일단 User 타입의 객체를 받아 타입을 좁혀 이 유저가 학생일 때에만 '등교 완료'를 콘솔에 출력한다.

위 코드는 겉으로 보기엔 큰 문제가 없어 보이지만 학생만 할 수 있는 기능이 점점 많아진다고 가정하면 매번 기능을 만들기 위해 함수를 선언할 때 마다 조건문을 이용해 타입을 좁혀야 하기 때문에 결국 매우 불편해질 것. 게다가 타입을 좁히는 코드는 매번 작성하게 되어 중복이 발생

이럴 때 제네릭 인터페이스를 이용하면 아래와 같이 단순화할 수 있다.

```ts
interface Student {
  type: 'student',
  school: string,
};

interface Developer {
  type: 'developer',
  skill: string,
};

interface User<T> {
  name: string,
  profile: T,
};

function goToSchool(user: User<Student>) {
  const school = user.profile.school;
  console.log(`${school}로 등교 완료`);
}

const developerUser: User<Developer> = {
  name: '박성현',
  profile: {
    type: 'developer',
    skill: 'TypeScript',
  }
}

const studentUser: User<Student> = {
  name: '홍길동',
  profile: {
    type: 'student',
    school: '배재학당',
  }
}
```

이렇게 하면 `goToSchool()`의 파라미터 타입을 `User<Student>` 처럼 정의해 학생 유저만 이 함수의 인수로 전달하도록 제한할 수 있다.<br>

결과적으로 함수 내부에서 타입을 좁힐 필요가 없어지므로 코드가 훨씬 간결해지는 효과를 보게 됨<br>

## 제네릭 클래스

마지막이다!

예시와 함께 살펴보기 위해 먼저 제네릭이 아닌 간단한 `Number` 타입의 리스트를 생성하는 클래스를 하나 만든다.

```ts
class NumberList {
  constructor(private list: number[]) {}

  push(data: number) {
    this.list.push(data);
  }

  pop() {
    return this.list.pop();
  }

  print() {
    console.log(this.list);
  }
}

const numberList = new NumberList([1, 2, 3]);
```

`list` 필드를 `private`로 설정해 클래스 내부에서만 접근할 수 있도록 만들고, 생성자에서 필드 선언과 함께 초기화했고

새로운 요소를 추가하는 `push()`, 제거하는 `pop()`, 출력하는 `print()` 메서드도 만들었음<br>

근데 만약 이 때 `StringList` 클래스도 하나 더 필요하다면... 제네릭을 사용하지 않으면 어쩔 수 없이 새로운 클래스를 하나 더 만들어줘야 한다.

```ts
class StringList {
  constructor(private list: string[]) {}

  push(data: string) {
    this.list.push(data);
  }

  pop() {
    return this.list.pop();
  }

  print() {
    console.log(this.list);
  }
}

const stringList = new StringList(['1','2','3']);
```

모든 리스트에 메서드가 새롭게 추가된다거나 동작이 수정되는 일이 생긴다면 아주 끔찍하노

이럴 때 제네릭 클래스로 타입을 싹 호환시키자고

```ts
class List<T> {
  constructor(private list: T[]) {};

  push(data: T) {
    this.list.push(data);
  }

  pop() {
    return this.list.pop();
  }

  print() {
    cnsole.log(this.list);
  }

  const numberList = new List([1, 2, 3]);
  const stringList = new List(['1', '2', '3']);
}
```

클래스의 이름 뒤에 타입 변수(`T`)를 선언하면 제네릭 클래스가 된다. 생성자를 통해 `T`의 타입을 추론할 수 있기 떄문에 생성자에 인수로 전달하는 값이 있을 경우엔 `T`에 할당할 타입을 생략해도 된다.<br>

만약 직접 타입을 설정해주고 싶다면 이렇게 하면 됨.

```ts
class List<T> {
  constructor(private list: T[]) {}

  (...)
}

const numberList = new List<number>([1, 2, 3]);
const stringList = new List<string>(['1', '2', '3']);
```

## 프로미스와 제네릭

하나 더 있었다 ...

<h3>Promise 사용하기</h3>

`Promise`는 제네릭 클래스로 구현되어 있다. 따라서 새로운 `Promise`를 생성할 때 다음과 같이 타입 변수 `T`에 할당할 타입을 직접 설정해주면 해당 타입이 바로 `resolve` 결과 값의 타입이 된다.

```ts
const promise = new Promise<number>((resolve, reject) => {
  setTimeout(() => {
    // 결과값: 20
    resolve(20);
  },3000);
});

promise.then((response) => {
  // response는 number 타입
  console.log(response);
})

promise.catch((error) => {
  if(typeof error === 'string') {
    console.log(error);
  }
})
```

아쉽게도 `reject` 함수에 인수로 전달하는 값, 즉 실패의 결과 값 타입은 정의할 수 없다. 그냥 `unknown` 으로 고정되어 있기 때문에 `catch`에서 사용하려면 타입 좁히기를 통해 안전하게 사용하는 것이 권장됨<br>

만약 어떤 함수가 `Promise` 객체를 반환한다면 함수의 반환 값 타입을 위해 다음과 같이 할 수 있다.

```ts
function fetchPost() {
  return new Promise<Post>((resolve, reject) => {
    setTimeout(() => {
      resolve({
        id: 1,
        title: '게시글 제목',
        content: '게시글 본문',
      });
    }, 3000);
  })
}

// 더 직관적으로 반환 값 타입을 직접 명시해도 된다.
function fetchPost(): Promise<Post> {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve({
        id: 1,
        title: '게시글 제목',
        content: '게시글 본문',
      });
    }, 3000);
  })
}
```