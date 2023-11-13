# 타입 조작

<h3>타입 조작이란</h3>
기본 타입이나 별칭, 인터페이스로 만든 원래 존재하던 타입들을 상황에 따라 유동적으로 다른 타입으로 변환하는 TS의 독특한 기능<br>

사실 제네릭도 `Type Manipulation`에 포함되기는 하지만 내용이 방대해서 따로 떼어 알아본 것임

<br>

<figure>
  <img src="img/ts-manipulation.png">
  <figcaption>이번 시간에 알아볼 것들</figcaption>
</figure>

<br>

## 인덱스드 액세스 타입

인덱스를 이용해 다른 타입 내의 특정 프로퍼티의 타입을 추출하는 타입. 객체, 배열, 튜플에 사용할 수 있다.

<h3>객체 프로퍼티의 타입 추출하기</h3>

다음과 같이 게시글을 표현하는 객체 타입이 있다고 가정한다.

```ts
interface Post {
  title: string,
  content: string,
  author: {
    id: number,
    name: string,
  }
}

const post: Post = {
  title: '게시글 제목',
  content: '게시글 본문',
  author: {
    id: 1,
    name: '박성현',
  }
}
```

만약 이 때 이 게시글에서 작성자의 이름과 아이디를 붙여서 출력하는 어떤 함수가 하나 있어야 한다면 다음과 같이 해야함

```ts
function printAuthorInfo(author: { id: number, name: string }) {
  console.log(`${author.id} - ${author.name}`);
}
```

그런데 파라미터의 타입을 이렇게 정의하면 나중에 `Post` 타입의 `author` 프로퍼티의 타입이 다음과 같이 수정되면 파라미터의 타입도 그 때 마다 계속 수정해줘야 하는 불편함이 있다.

```ts
interface Post {
  title: string,
  content: string
  author: {
    id: number,
    name: string,
    age: number; // 나이가 추가됐네
  }
}

function printAuthorInfo(author: { id: number, name: string, age: number }) {
  // 파라미터에 age도 또 추가해야겠네
  console.log(`${author.id} - ${author.name}`);
}
```

이럴 때 인덱스드 액세스 타입을 이용해 `Post`에서 `author` 프로퍼티의 타입을 추출해 사용하면 편리하다.

```ts
interface Post {
  title: string,
  content: string,
  author: {
    id: number,
    name: string,
    age: number,
  }
}

function printAuthorInfo(author: Post['author']) {
  console.log(`${author.id} - ${author.name}`);
}
```

`Post['author']`는 `Post` 타입으로부터 `author` 프로퍼티의 타입을 추출한다. 그 결과 `author` 파라미터의 타입은 `{id: number, name: string, age: number}`가 된다.<br>

이 때 대고라호 속에 들어가는 `String Literal` 타입인 `author`를 인덱스라고 부른다. 그래서 인덱스를 이용해 특정 타입에 접근 가능하다고 하여 인덱스드 액세스 타입이라고 부름.<br>

**주의할 점**: 인덱스에는 값이 아니라 타입만 들어갈 수 있다. 따라서 다음과 같이 `author`를 문자열 값으로 다른 변수에 저장하고 다음과 같이 인덱스로 사용하려 하면 오류가 발생함.<br>

```ts
const authorKey = 'author';

function printAuthorInfo(author: Post[authorKey]) { // ❌ 폼나보이지만 안된다고~
  (...)
}
```

<br>

또 하나 더 있음<br>

인덱스에 존재하지 않는 프로퍼티 이름을 쓰면 오류가 발생함

```ts
function printAuthorInfo(author: Post['notExist']) { // 당연한거 아닌가요
  (...)
}
```

인덱스를 중첩해서 사용할 수도 있다.

```ts
interface Post {
  title: string,
  content: string,
  author: {
    id: number,
    name: string,
    age: number,
  }
}

function printAuthorInfo(author: Post['author']['id']) {
  // author의 파라미터 타입은 number
  console.log(`${author.id} - ${author.name}`);
}
```

근데 이렇게 하면 author.id, author.name은 어떻게 접근하지<br>

<h3>배열 요소의 타입 추출하기</h3>

인덱스드 액세스 타입은 객체 프로퍼티의 타입 뿐만 아니라 특정 배열의 요소 타입을 추출하는데에도 이용할 수 있다. 

```ts
type PostList = {
  title: string,
  content: string,
  author: {
    id: number,
    name: string,
    age: number,
  }
}

// 인덱스드 타입을 이용해 다음과 같이 이 `PostList` 배열 타입에서 하나의 요소의 타입만 뽑아올 수 있다.

const post: PostList[number] = {
  title: '게시글 제목',
  content: '게시글 본문',
  author: {
    id: 1,
    name: '성현',
    age: 26
  }
}
```

`PostList[number]`는 `PostList` 배열 타입으로부터 요소의 타입을 추출하는 인덱스드 액세스 타입이다. 이렇듯 배열의 요소 타입을 추출할 때에는 인덱스에 `number` 타입을 넣어주면 된다.<br>

또 인덱스에 다음과 같이 `Number Literal` 타입을 넣어도 된다. 숫자와 관계없이 모두 `Number` 타입을 넣은 것과 동일하게 동작한다

```ts
const post: PostList[0]  = {
  title: '게시글 제목',
  content: '게시글 본문',
  author: {
    id: 1,
    name: '성현',
    age: 26,
  }
}
```

<br>

<h3>튜플의 요소 타입 추출하기</h3>

튜플의 각 요소들 또한 인덱스드 액세스 타입으로 쉽게 추출할 수 있다.

```ts
type Tup = [number, string, boolean];

type Tup0 = Tup[0]; // number

type Tup1 = Tup[1]; // string

type Tup2 = Tup[2]; // boolean

type Tup3 = Tup[number] // number | string | boolean
```

**주의할 점**: 튜플 타입에 인덱스드 액세스 타입을 사용할 때 인덱스에 `number` 타입을 넣으면 마치 튜플을 배열처럼 인식해 배열 요소의 타입을 추출하게 된다.

## keyof & typeof 연산자

`keyof` 연산자는 객체 타입으로부터 프로퍼티의 모든 key들을 `String Literal Union` 타입으로 추출하는 연산자이다.

```ts
// Obj의 Key를 타입으로 추출
interface Person {
  name: string,
  age: number,
}

function getPropertyKey(person: Person, key: 'name' | 'age') {
  return person[key];
}

const person: Person = {
  name: '박성현',
  age: 26,
}
```

`Person` 객체 타입을 정의하고 해당 타입을 갖는 변수 하나를 선언

`getPropertyKey()` 함수는 두 개의 파라미터를 가지며 두 번째 파라미터 `key`에 해당하는 프로퍼티의 값을 첫 번째 파라미터 `person`에서 꺼내 반환한다.

이 때 `key`의 타입을 'name' 또는 'age'로 정의했는데 이렇게 정의하면 나중에 `Person` 타입에 새로운 프로퍼티가 추가되거나 수정됐을 경우 얘도 계속 바꿔줘야함 <- 불편하다.<br>

이럴 때 `keyof` 연산자를 이용해야 함

```ts
interface Person {
  name: string,
  age: number,
  location: string, // 얘가 추가됐음
}

function getPropertyKey(person: Person, key: keyof Person) {
  return person[key];
}

const person: Person = {
  name: '박성현',
  age: 26,
  location: 'SUWON'
}
```

<br>

**주의할 점**: keyof 연산자는 오직 타입에만 적용할 수 있는 연산자임. 위에서 `keyof Person`이 아니라 `ketof person`으로 입력하거나 할 경우엔 에러가 발생

<br>

<h3>`typeof`와 `keyof` 함께 사용하기</h3>

typeof 연산자는 js에서 특정 값의 타입을 문자열로 반환하는 연산자였다. 그러나 다음과 같이 타입을 정의할 때 사용하면 특정 변수의 타입을 추론하는 기능도 가지고 있다.

```ts
interface Person {
  name: string,
  age: number,
  location: string,
}

function getProperty(person: Person, key: keyof typeof person) { // typeof person
  return person[key];
}

const person: Person = {
  name: '박성현',
  age: 26,
  location: 'SUWON'
}
```

<br>

## 맵드 타입

기존의 객체 타입을 기반으로 새로운 객체 타입을 만드는 기능. JS의 `map()` 함수를 타입에 적용한 것과 같은 효과를 가진다.

<h3>기본 모양</h3>

```ts
{ [ P in K ]: T }
{ [ P in K ]?: T }
{ readonly [ P in K ]: T }
{ readonly [ P in K ]: T }
```

유저 정보를 관리하는 간단한 프로그램의 일부분을 만든다고 가정한다.

```ts
interface User {
  id: number,
  name: string,
  age: number,
}

function fetchUser(): User {
  return {
    id: 1,
    name: '박성현',
    age: 26,
  }
}


function updateUser(user: User) {
  (...)
}
```

`updateUser()` 함수는 수정된 유저 객체를 받아 유저 정보를 수정한다.

따라서 유저 정보를 수정하려면 다음과 같이 이 함수를 호출하고 여러 개의 정보 중 수정하고 싶은 프로퍼티만 전달해주면 된다.

```ts
(...) // 위의 코드 그대로 작성했다고 침

updateUser({ // Error
  age: 25
})
```

근데 `updateUser` 함수의 파라미터 타입이 `User`이기 때문에 유저 객체를 그대로 보내야만 하는 상황임. 이러면 새로운 타입을 만들어서 넣어줘야하는데 이건 너무 불필요한 반복임.

이럴 때 맵드 타입을 이용할 수 있다

```ts
interface User {
  id: number,
  name: string,
  age: number,
}

interface PartialUser {
  id?: number,
  name?: string,
  age?: number,
}

// 이런 식으로 작성할 수 있다.

interface PartialUser {
  [key in 'id' | 'name' | 'age'] ?: User[key];
}
```

문법을 자세히 살펴보면 다음과 같다.

`[key in 'id' | 'name' | 'age']`는 이 객체 타입은 key가 한번은 id, 한번은 name, 한번은 age가 된다는 뜻이다. 따라서 다음과 같이 3개의 프로퍼티를 갖는 객체 타입으로 정의됨

- `key`가 `id`일 때 -> `id: User[id]` -> `id: number`
- `key`가 `name`일 때 -> `name: User[name]` -> `name: string`
- `key`가 `age`일 때 -> `age: User[age]` -> `age: number`

여기에 대괄호 뒤에 선택적 프로퍼티를 의미하는 물음표(`?`) 키워드가 붙어있으므로 모든 프로퍼티가 선택적 프로퍼티가 되어 결론적으로 위에 써있는 중복 코드가 같은 타입이 되는 것이다.

여기서 이전 시간에 배운 `keyof`를 이용하면 `S E X Y C O D E`가 된다.

```ts
interface User {
  id: number,
  name: string,
  age: number,
}

type PartialUser = {
  [key in keyof User]?: User[key]; // kia
}

// 맵드 타입을 이용해 모든 프로퍼티가 읽기 전용 프로퍼티가 된 타입을 만드는 예시
type ReadOnlyUser = {
  readonly [key in keyof User]: User[Key];
}
```

<br>

## 템플릿 리터럴 타입

타입 조작 기능들 중 가장 단순한 기능으로 템플릿 리터럴을 이용해 특정 패턴을 갖는 `String` 타입을 만드는 기능

예시와 함께 살펴봅시다

```ts
type Color = 'red' | 'black' | 'green';
type Animal = 'dog' | 'cat' | 'chicken';

type ColoredAnimal = `red-dog` | `red-cat` | `red-chicken` | `black-dog` | `hot-dog` | ...
```

`Color`와 `Animal`은 각각 3개의 `String Literal` 타입으로 이루어진 `Union` 타입이다. 그리고 `ColoredAnimal`은 `Color`와 `Animal`을 조합해 만들 수 있는 모든 가짓수의 `String Literal` 타입으로 이루어진 `Union` 타입이다.

`Color`나 `Animal` 타입에 `String Literal` 타입이 추가되어 경우의 수가 많아질 수록 `ColoredAnimal`에 입력해야할 값이 계속 많아지게 될 것

이럴 때 템플릿 리터럴 타입을 사용하자

```ts
type ColoredAnimal = `${Color}-${Animal}`;
```