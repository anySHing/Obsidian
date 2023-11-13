# 유틸리티 타입

TS에서 자체적으로 제공하는 특수한 타입들이다. 지금까지 배웠던 제네릭, 맵드 타입, 조건부 타입 등의 타입 조작 기능을 이용해 실무에서 자주 사용되는 유용한 타입들을 모아놓은 것을 의미

[여기](https://www.typescriptlang.org/docs/handbook/utility-types.html)에서 다양한 유틸리티 타입들을 확인할 수 있다.

## Partial<T>

가장 처음으로 살펴볼 타입은 `Partial<T>`

특정 객체 타입의 모든 프로퍼티를 선택적 프로퍼티로 변환한다. 따라서 기존 객체 타입에 저으이된 프로퍼티들 중 일부분만 사용할 수 있도록 도와주는 타입이다.

간단한 블로그 플랫폼의 일부를 직접 구현한다고 가정.

일단 다음과 같이 게시글 하나를 표현하는 타입을 먼저 선언한다.

```ts
interface Post {
  title: string,
  tags: string[].
  content: string,
  thumbnailURL?: string,
}
```

다음으로 *임시 저장 기능*이 필요하다고 가정한다. 그럼 다음과 같이 임시 저장된 게시글을 변수로 저장할 수 있어야 함.

```ts
interface Post {
  title: string,
  tags: string[],
  content: string,
  thumbnailURL?: string,
}

const draft: POST = { // ❌ tags 프로퍼티가 없다.
  title: '제목은 나중에 짓자',
  content: '초안 ...'
}
```

위와 같이 게시글의 일부 정보가 아직 설정되어 있지 않은 임시 저장 게시글의 경우에도 변수에 저장할 수 있어야 하는데 해당 변수를 Post 타입으로 저장하면 오류가 발생하게 된다.

그렇다고 임시 저장 게시글 기능을 위해 Post 타입의 모든 프로퍼티를 선택적 프로퍼티로 설정하는 것도 곤란하다. 진짜 작성이 완료되어 화면에 렌더링될 게시글들은 이 모든 프로퍼티를 전부 가지고 있어야만 하기 때문

이럴 때 `Partial<T>` 유틸리티 타입을 이용하면 좋다.

```ts
interface Post {
  title: string,
  tags: string[],
  content: string,
  thumbnailURL?: string,
}

const draft: Partial<Post> = {
  title: 'asdfasdf',
  content: 'asdfasdf',
}
```

`Partial<T>` 타입은 타입 변수 `T`로 전달한 객체 타입의 모든 프로퍼티를 전부 선택적 프로퍼티로 변환한다. 따라서 `Partial<Post>` 타입은 모든 프로퍼티가 선택적 프로퍼티가 된 `Post` 타입과 같다.

<br>

<h3>Partial&lt;T&gt; 구현하기</h3>

이번에는 `Partial<T>` 유틸리티 타입을 직접 구현해보자

일단 하나의 타입 변수 T를 사용하는 제네릭 타입인 것은 확실하다.

```ts
type Partial<T> = any
```

다음으로는 `T`에 할당된 객체 타입의 모든 프로퍼티를 선택적 프로퍼티로 바꿔줘야 한다. 기존 객체 타입을 다른 타입으로 변환하는 타입은 맵드 타입이었다. 따라서 맵드 타입을 이용해 다음과 같이 수정해야한다.

```ts
type Partial<T> = {
  [key in keyof T]?: T[key];
}
```

<br>

## Required<T>

특정 객체 타입의 모든 프로퍼티를 필수 프로퍼티로 변환한다.

이번에는 썸네일이 반드시 있어야 하는 게시글이 하나 필요하다고 가정한다.

```ts
interface Post {
  title: string;
  tags: string[];
  content: string;
  thumbnailURL?: string;
}

(...)

// 반드시 썸네일 프로퍼티가 존재해야 하는 게시글
const withThumbnailPost: Post = {
  title: "한입 타스 후기",
  tags: ["ts"],
  content: "",
  thumbnailURL: "https://...", // 이 부분이 빠져도 오류가 발생하지 않음
};
```

`withThumnailPost`는 모종의 이유(마케팅 등)로 반드시 썸네일이 포함된 게시글이어야 한다. 그런데 `Post` 타입의 `thumbnailURL` 프로퍼티가 현재 선택적 프로퍼티로 설정되어 있기 때문에 다음과 같이 실수로 주석 처리 하거나 삭제 한다고 해도 타입 오류가 발생하지 않는다.

이런 상황에 `Required<T>` 타입을 이용한다.

```ts
interface Post {
  title: string,
  tags: string[],
  content: string,
  thumbnailURL?: string,
}

const withThumbnailPost: Required<Post> = { // ❌
  title: '한입TS후기',
  tags: ['ts'],
  content: '',
  // thumbnailURL: 'https://www...',
}
```

`Required<Post>`는 `Post` 타입의 모든 프로퍼티가 필수 프로퍼티로 변환된 객체 타입이다. 따라서 위 코드처럼 `thumbnailURL` 프로퍼티를 생략할 경우 오류가 발생하게 된다.

<h3>Required&lt;T&gt; 타입 직접 구현하기</h3>

일단 기존의 모든 프로퍼티를 포함하는 제네릭 맵드 타입으로 만들어준다.

```ts
type Required<T> = {
  [key in keyof T]: T[key];
};
```

그리고나서 모든 프로퍼티가 필수 프로퍼티가 되도록 만들어야 한다. 모든 프로퍼티를 필수 프로퍼티로 만든다는 말은 반대로 바ㅜ꺼보면 모든 프로퍼티에서 `선택적`이라는 기능을 제거하는 것과 같다. 따라서 다음과 같이 `-?`를 프로퍼티 이름 뒤에 붙여주면 된다.

```ts
type Required<T> = {
  [key in keyof T]-?: T[key];
}
```

`-?`는 `?`가 붙어있는 선택적 프로퍼티가 있을 경우 `?`를 제거하라는 의미

<br>

## Readonly

특정 객체 타입의 모든 프로퍼티를 읽기 전용 프로퍼티로 변환한다.

절대 내부를 수정할 수 없는 보호된 게시글이 하나 필요하다고 가정한다.

```ts
interface Post {
  title: string,
  content: string,
  tags: string[],
  thumbnailURL?: string;
}

const readonlyPost: Post = {
  title: '보호된 게시글',
  tags: [],
  content: '',
};

readonlyPost.content = '해킹당함'
```

변수 `readonlyPost`는 보호받아야 하는 게시글로 절대 객체 내부의 값을 수정하지 못하게 막아야 한다고 가정. 그러나 `Post` 타입의 모든 프로퍼티가 `readonly` 설정이 되어있지 않기 때문에 지금은 보호받을 수가 없다.

이럴 때 `Readony<T>`를 사용한다.

```ts
interface Post {
  title: string,
  content: string,
  tags: string[],
  thumbnailURL?: string;
}

const readonlyPost: Readonly<Post> = {
  title: '보호된 게시글',
  tags: [],
  content: '건들 수 있으면 건드려봐',
  thumbnailURL: 'https://www',
}

readonlyPost.content = 'qweasd'; // ❌
```

<h3>Readonly&lt;T&gt; 구현하기</h3>

```ts
type Readonly<T> = {
  readonly [key in keyof T]: T[key];
}
```

<br>

## Pick<T, K>

특정 객체 타입으로부터 특정 프로퍼티만을 골라내는 기능을 갖고있음

ex) Pick 타입에 T가 name, age가 있는 객체 타입이고, K가 name 이라면 결과는 name만 존재하게 되는 것

옛날에 작성된 포스트가 하나 존재한다고 가정

```ts
interface Post {
  title: string,
  tags: string[],
  content: string,
  thumbnailURL?: string,
}

const legacyPost: Post = { // ❌
  title: '',
  content: '',
}
```

이 때 `legacyPost`에 저장되어 있는 게시글은 태그나 썸네일 기능이 추가되기 이전에 만들어진 게시글이라고 한다면, 타입이 `Post`로 설정되어 있을 경우 `tags` 프로퍼티를 갖고 있지 않기 때문에 에러가 발생하게 됨

이럴 때 Pick을 사용한다.

```ts
interface Post {
  title: string,
  tags: string[],
  content: string,
  thumbnailURL?: string,
}

const legacyPost: Pick<Post, 'title' | 'content'> = { // 추출된 타입: { title: string, content: string}
  title: '',
  content: '',
};
```

변수 `legacyPost`의 타입으로 `Pick<Post, 'title' | 'content'`를 정의했다. 따라서 이 때 타입 변수 `T`에는 `Post`, `K`에는 `'title' | 'content'`가 각각 할당된다.

-> `Post` 타입으로부터 `'title'`과 `'post'` 프로퍼티만 추출한 셈

<h3>Pick&lt;T, K&gt; 구현하기</h3>

일단 2개의 타입 변수 `T`와 `K`를 사용하는 타입이므로 다음과 같이 정의한다.

```ts
type Pick<T, K> = any;
```

그 다음 `T`로 부터 `K` 프로퍼티만 추출한 객체 타입을 만들어야 하므로 다음과 같이 맵드 타입으로 정의한다.

```ts
type Pick<T, K> = {
  [key in K]: T[key];
}
```

마지막ㅇ로는 `K`가 `T`의 key로만 이루어진 `String Literal Union` 타입임을 보장해줘야 한다. 따라서 다음과 같이 제약을 추가해야 함

```ts
type Pick<T, K extends keyof T> = {
  [key in K]: T[key];
}
```

<br>

## Omit<T, K>

특정 객체 타입으로부터 특정 프로퍼티만을 제거하는 기능을 가지고 있다.

ex) `Omit` 타입에 `T`가 name, age를 갖고 있는 객체 타입이고, `K`가 name 이라면 결과는 name을 제외한 age만 갖고있는 객체 타입이 된다.

제목이 없는 게시글도 존재할 수 있다고 가정

```ts
interface Post {
  title: string,
  tags: string[],
  content: string,
  thumbnailURL?: string,
}

const noTitlePost: Post = { // ❌
  content: '',
  tags: [],
  thumbnailURL: '',
};
```

`title` 프로퍼티가 없으므로 빨간 줄이 뜨게 될 것

다음과 같이 `Omit`을 이용해 `Post` 타입으로부터 `title` 프로퍼티를 제거한 타입으로 변수의 타입을 정의한다.

```ts
const noTitlePost: Omit<Post, 'title'> = {
  content: '',
  tags: [],
  thumbnailURL: '',
}
```

<h3>Omit&lt;T, K&gt; 구현하기</h3>

2개의 타입 변수를 사용하는 제네릭 타입이므로 일단 다음과 같이 정의한다.

```ts
typw Omit<T, K> = any;
```

그 다음 앞서 Pick 타입에서 한 것과 같이 `K`에 제약을 추가한다.

```ts
type Omit<T, K extends keyof T> = any;
```

그리고 이 때 앞서 만든 Pick 타입을 이용해 다음과 같이 완성한다.

```ts
type Omit<T, K extends keyof T> = Pick<T, Exclude<keyof T, K>>;
```

이게 뭔소린가 싶다

1. `T`는 `Post`, `K`는 `title`이라고 가정
2. 이 때 `keyof T`는 `'title' | 'content' | 'tags' | 'thumbnailURL'` 이므로 `Pick<T, Exclude<keyof T, K>>` === `Pick<T, Exclude<'title' | 'content' | 'tags' | 'thumbnailURL', 'title'>>`이 된다.
3. `Exclude`는 `T`로 부터 `K`를 제거하는 기능을 갖고있다. 따라서 한번 더 변환하면 `Pick<Post, 'content' | 'tags' | 'thumbnailURL'>`이 된다.
4. `Pick<Post, 'content' | 'tags' | 'thumbnailURL'>` === `'content' | 'tags' | 'thumbnailURL'` 이므로 결과적으로 `Omit<Post, 'title'>` === `'content' | 'tags' | 'thumbnailURL`이 된다.

<br>

## Record<K, V>

화면 크기에 따라 3가지 버전의 썸네일을 지원하도록 가정하고 `Thumbnail` 타입을 별도로 정의한다.

```ts
type Thumbnail = {
  large: {
    url: string;
  },
  medium: {
    url: string;
  },
  small: {
    url: string;
  },
}
```

그런데 여기에 `watch` 버전이 또 추가되어야 한다면 다음과 같이 똑같이 생긴 프로퍼티를 하나 더 추가해줘야 한다. 앞으로 버전이 많아질 수록 계속해서 중복 코드가 발생하게 된다.

```ts
type Thumbnail = {
  large: {
    url: string;
  },
  medium: {
    url: string;
  },
  small: {
    url: string;
  },
  watch: {
    url: string;
  }
}
```

이럴 때 `Record`를 이용한다. 다음과 같이 `K`에는 어떤 프로퍼티들이 있을지 `String Literal Union` 타입을 할당하고 `V`에는 프로퍼티의 값 타입을 할당한다.

```ts
type Thumbnail = Record<'large' | 'medium' | 'small', { url: string }>
```

위 `Record` 타입은 `K`에 `'large' | 'medium' | 'small'`이 할당되었으므로 large, medium, small 프로퍼티가 있는 객체 타입을 정의한다. 그리고 각 프로퍼티 `value`의 타입은 `V`에 할당한 `{ url: string; }`이 된다.

<h3>Record&lt;T&gt; 구현하기</h3>

```ts
type Record<K extends keyof any, V> = {
  [key in K]: V;
}
```

## Exclude<T, K>

`T`로 부터 `K`를 제거하는 기능을 갖고있다.

```ts
type A = Exclude<string | boolean, string>; // boolean

// 직접 구현하면 다음과 같다

type Exclude<T, K> = T extends K ? never : T;
```

<br>

## Extract<T, K>

`T`로 부터 `K`를 추출하는 기능을 갖고있다.

```ts
type B = Extract<string | boolean, boolean>; // boolean

// 직접 구현하면 다음과 같다.

type Extract<T, K> = T extends K ? T : never;
```

<br>

## ReturnType<T>

타입 변수 `T`에 할당된 함수 타입의 리턴 값 타입을 추출하는 타입이다.

```ts
type ReturnType<T extends (...args: any) => any> = T extends (...args: any => infer R) ? R : never;

function funcA() {
  return 'hello';
}
function funcB() {
  return 10;
}

type ReturnA = ReturnType<typeof funcA>; // string
type ReturnB = ReturnType<typeof funcB>; // number
```