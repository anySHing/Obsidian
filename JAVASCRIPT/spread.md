# Rest Parameters

    ※ 용어가 그리 중요하진 않지만 적어둠
    	* Parameter == 매개변수 == 함수 정의 시 선언되는 '변수'
    	* Arguments == 전달인자(인수) == 함수 호출 시 실제로 들어가는 '값'

<br/>

## 정해지지 않은 수의 인수를 받는 방법

```js
const sum = (a, b) => {
	return a + b;
};

console.log(sum(1, 2, 3, 4, 5)); // 3, 에러가 나지 않는다.
```

여분의 매개변수는 그 값들을 담을 배열 이름을 마침표 3개(...) 뒤에 붙여주면 함수 선언부에 포함시킬 수 있다.

=> 남아있는 매개변수들을 한 데 모아 배열에 집어넣어라. 라는 뜻

아래 예시에서는 모든 인수가 배열 arg에 모인다.

```js
const sumAll = (...args) => {
	let sum = 0;
	args.forEach((arg) => {
		sum += arg;
	});
	return sum;
};

console.log(sumAll(1)); // 1
console.log(sumAll(1, 2)); // 3
console.log(sumAll(1, 2, 3)); // 6
```

앞 부분의 매개변수는 변수로, 남아있는 매개변수들은 배열로 모을 수도 있다.

아래 예시에서는 처음 두 인수는 변수에, 나머지 인수들은 titles라는 배열에 할당된다.

```js
const showName = (firstName, lastName, ...titles) => {
	console.log(`${firstName} ${lastName}`); // SEONGHYEON PARK

	// 나머지 인수들은 배열 titles의 요소가 된다.
	// titles = [26, 'SUWON'];

	console.log(titles[0]); // 26
	console.log(titles[1]); // 'SUWON'
	console.log(titles.length); // 2
};

showName('SEONGHYEON', 'PARK', 26, 'SUWON');
```

**주의!! 나머지 매개변수는 항상 마지막에 있어야 한다.**

나머지 매개변수는 남아있는 인수를 모으는 역할을 하므로 아래 예시에선 에러가 발생한다.

```js
const f = (args1, ...rest, arg2) => { ... } // ...rest 뒤에 파라미터가 더 있으면 안됨!
```

---

## arguments 객체

유사 배열 객체인 arguments를 사용하면 인덱스를 사용해 인수에 접근할 수 있다.

```js
function showName() {
	console.log(arguments.length);
	console.log(arguments[0]);
	console.log(arguments[1]);

	// arguments는 iterable 객체이므로 for...of 사용 가능
	for (let arg of arguemnts) console.log(arg);
}

showName('SH', 'P');
/*
	2
	SH
	P
*/

showName('SH');
/* 
	1
	SH
	undefined
 */
```

**주의!!**

**arrow function에서는 arguments를 지원하지않음**

**배열같은거긴 하지만 어쨌든 배열은 아니라 배열 메서드는 사용할 수 없음 map() 같은거**

**인수 전체를 담기 때문에 (...)처럼 일부만 사용하는 것은 불가능하다.**

~~옛날거라 쓸 일은 없고 봤을 때 알아볼 수만 있으면 되지 않을까?~~

<br />

# Spread

이건 반대로 배열을 통째로 매개변수에 넘겨주는 방식임

가장 큰 수를 반환하는 Math.max()로 예를 들어보면

```js
console.log(Math.max(3, 5, 1)); // 5
```

<br />
만약 배열 [3,5,1]을 Math.max()에 넣고 돌리고 싶다면?

<br />

```js
const arr = [3, 5, 1];
console.log(Math.max(arr); // Error, max()의 파라미터는 number만 가능함
```

<br />
일일이 하나하나 arr[0] arr[1] 적고 앉아있기엔 너무 추하다

<br />
심지어 배열의 길이가 동적으로 변한다면 불가능이라고 볼 수 있음

이럴 때 spread를 사용한다.

<br />

**사용법**

```js
let arr = [3, 5, 1];
console.log(Math.max(...arr)); // 5 (배열의 인덱스를 하나한 펼쳐줌)
```

<br />
여러 개를 전달할 수도 있다.

<br />

```js
let firstArr = [1, -2, 3, -4];
let secondArr = [5, -6, 7, -8];

console.log(Math.max(...firstArr, ...secondArr)); // 7
// == Math.max(1, -2, 3, -4, 5, -6, 7, -8);
```

<br />
평범한 값과 혼합해서 사용하는 것도 가눙함

<br />

```js
let arr = [3, 4, 5];

console.log(Math.max(1, 2, ...arr)); // 5
```

<br />
배열을 합치는데도 사용할 수 있다.

<br />

```js
let arr1 = [1, 2, 3];
let arr2 = [4, 5, 6];

const mergedArr = [...arr1, ...arr2]; // [1,2,3,4,5,6];
```

<br />

앞선 예시들은 '배열'을 대상으로 사용한 Spread

그런데 배열이 아니더라도 Iterable 객체이면 Spread 가능

<br />

```js
let str = 'Hello';

console.log([...str]); // H, e, l, l, o
```

Spread 문법은 for...of와 같은 방식으로 내부에서 Iterator를 사용해 요소를 수집한다.

string에 for...of를 사용하면 문자열을 구성하는 문자가 반환되는... 어쩌고 저쩌고..

<br>
Array.from은 Iterable 객체인 문자열을 배열로 바꿔주기 때문에 동일한 결과를 얻을 수 있음

```js
let str = 'HELLO';

// Array.from()은 Iterable을 Array로 바꿔준다
console.log(Array.from(str)); // H, E, L, L, O
```

<br>

하지만<br>
Array.from()은 **'유사 배열 객체'** 에도 사용할 수 있음<br>
Spread는 Iterable에 **'만'** 사용할 수 있음

<br><br>

# 요약

`...` 은 나머지 매개변수나 스프레드 문법으로 사용할 수 있다.

<br>

__구분 방법__
- 함수 매개변수의 끝에 `...` 이 있으면 인수 목록의 나머지를 배열로 모아주는 **Rest Param**
- 함수 호출 시 `...` 이 있으면 **Spread**

<br>

__사용 패턴__

- Rest Param: 인수 개수에 제한이 없는 함수를 만들 때
- Spread: 다수의 인수를 받는 함수에 배열을 전달할 때
