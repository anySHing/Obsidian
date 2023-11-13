# 인터페이스

**인터페이스**: 타입 별칭과 동일하게 타입에 이름을 지어주는 또 다른 문법

예를 들어 간단한 `Person` 객체의 타입을 정의한다면 다음과 같이 할 수 있다.

```ts
interface Person {
  name: string,
  age: number,
}
```

이렇게 정의한 인터페이스를 타입 주석과 함께 사용해 타입을 정의할 수 있다.

<br>

```ts
const person: Person = {
  name: '성현',
  age: 26,
}
```

인터페이스는 타입 별칭과 문법만 조금 다를 뿐 기본적인 기능은 거의 같다고 볼 수 있음.

<br>

<h3>선택적 프로퍼티</h3>

인터페이스에서도 동일한 방법으로 선택적 프로퍼티 설정이 가능

```ts
interface Person {
  name: string,
  age?: number,
};

const person: Person {
  name: '성현',
}
```

<br>

<h3>읽기 전용 프로퍼티</h3>

읽기 전용 프로퍼티 또한 동일하다

```ts
interface Person {
  readonly name: string,
  age?: number,
}

const person: Person {
  name: '성현',
}

person.name = '홍길동'; // ❌
```

<br>

<h3>메서드 타입 정의하기</h3>

다음과 같이 메서드의 타입을 정의하는 것 또한 가능하다.

```ts
interface Person {
  readonly name: string,
  age?: number,
  sayHi: () => void;
}
```

함수 타입 표현식을 이용해 sayHi 메서드의 타입을 정의했다.

함수 타입 표현식 말고 호출 시그니쳐를 사용해 정의할 수도 있음

```ts
interface Person {
  readonly name: string,
  age?: number,
  sayHi(): void;
}
```

<br>

<h3>메서드 오버로딩</h3>

함수 타입 표현식으로 메서드의 타입을 정의하면 메서드의 오버로딩 구현은 불가능하다.

```ts
interface Person {
  readonly name: string,
  age?: number,
  sayHi: () => void,
  sayHi: (a: number, b: number) => void; // ❌
}
```

<br>

호출 시그니쳐를 사용할 경우에는 메서드 오버로딩이 가능함.

```ts
interface Person {
  readonly name: string,
  age?: number,
  sayHi(): void,
  sayHi(a: number): void, // OK
  sayHi(a: number, b: number): void, // OK
}
```

<br>

<h3>하이브리드 타입</h3>

인터페이스 또한 함수이자 일반 객체인 하이브리드 타입을 정의할 수 있다.

```ts
interface Func2 {
  (a: number): string,
  b: boolean;
}

const func: Func2 = (a) => 'hello';
func.b = true;
```

<h3>주의할 점 1.</h3>

인터페이스는 대부분의 상황에 타입 별칭과 동일하게 동작하지만 몇가지 차이점이 존재한다. 타입 별칭에서는 다음과 같이 `Union`이나 `intersection` 타입을 정의할 수 있었던 반면 인터페이스에서는 할 수 없다.

```ts
type Type1 = number | string;
type Type2 = number & string;

interface Person {
  name: string,
  age: number,
} | number // ❌
```

따라서 인터페이스로 만든 타입을 유니온 또는 인터섹션으로 사용해야 한다면 다음과 같이 타입 별칭과 함께 사용하거나 타입 주석에서 직접 사용해야 한다.

```ts
type Type1 = number | string | Person;
type Type2 = number & string & Person;

const person: Person & string {
  name: '성현',
  age: 26,
}
```

## 인터페이스 확장

**인터페이스 확장**: 하나의 인터페이스를 다른 인터페이스들이 상속받아 중복된 프로퍼티를 정의하지 않도록 도와주는 문법

<br>

다음과 같이 3개의 타입이 정의되어 있다고 가정

```ts
interface Animal {
  name: string,
  age: number,
};

interface Dog {
  name: string,
  age: number,
  isBark: boolean,
};

interface Cat {
  name: string,
  age: number,
  isScratch: boolean,
};

interface Chicken {
  name: string,
  age: number,
  isKOKYO: boolean,
}
```

각 타입들을 살펴보면 Animal 타입을 기반으로 `Dog`, `Cat`, `Chicken`이 각각 추가적인 프로퍼티를 갖고있는 형태임을 알 수 있다.

<br>

이렇게 특정 인터페이스를 기반으로 여러 개의 인터페이스가 파생되는 경우 중복 코드가 발생할 수 있는데, 이럴 때 인터페이스의 확장 기능을 이용하면 좋다.

```ts
interface Animal {
  name: string,
  color: string,
};

interface Dog extends Animal {
  breed: string,
};

interface Cat extends Animal {
  isScratch: boolean,
};

interface Chicken extends Animal {
  isKOKYO: boolean,
};
```

<br>

<h3>프로퍼티 재정의하기</h3>

다음과 같이 확장과 동시에 프로퍼티의 타입을 재정의하는 것 또한 가능하다.

```ts
interface Animal {
  name: string,
  color: string,
};

interface Dog extends Animal {
  name: 'dolodlE', // 타입 재정의
  breed: string
}
```

`Dog` 타입은 `Animal` 타입을 확장하며 동시에 `name` 프로퍼티의 타입을 `string` 타입에서 'doldolE' 라는 `string literal` 타입으로 재정의했다.

**주의할 점**: 프로퍼티를 재정의할 때 원본 타입을 A, 오버라이딩된 타입을 B라고 한다면 반드시 A가 B의 부모여야 한다.

<br>

<h3>타입 별칭을 확장하기</h3>

인터페이스는 인터페이스 뿐만 아니라 타입 별칭으로 정의된 객체도 확장할 수 있다.

```ts
type Animal {
  name: string,
  color: string,
};

interface Dog extends Animal {
  breed: string,
}
```

<br>

<h3>다중 확장</h3>

여러 개의 인터페이스를 확장하는 것 또한 가능하다.

```ts
interface DogCat extends Dog, Cat {}

const dogCat: DogCat = {
  name: '',
  color: '',
  breed: '',
  isScratch: true,
}
```

<br>

## 인터페이스 선언 합치기

<h3>선언 합침</h3>

`타입 별칭`은 동일한 스코프 내에 중복된 이름으로 선언할 수 없다고 했었다.

근데 인터페이스는 가능함

```ts
type Person = {
  name: string,
};

type Person = { // ❌
  age: number, // ❌
} // ❌

interface Person {
  name: string,
};

interface Person {
  age: number, // OK
}
```

이게 가능한 이유는 중복된 이름의 인터페이스는 결국 모두 하나로 합쳐지기 때문이다.

따라서 위 코드에 선언한 `Person` 인터페이스들은 결국 합쳐져

```ts
interface Person {
  name: string,
  age: number,
}
```

와 같은 형태가 된다.