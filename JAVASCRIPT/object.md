# 객체
자바스크립트에 존재하는 자료형은 8개

1. number

2. BitInt

3. string

4. boolean

5. null

6. undefined

7. symbol
---

여기까지는 원시타입
---

8. object

자바스크립트의 객체는 key:value 형태로 구성되어있음

## 만드는 방식
    let user = new Object(); // '객체 생성자'
    let user = {}; // '객체 리터럴' <- 주로 이 방식을 사용함. 
    

## 리터럴과 프로퍼티
    let user = {
        name:'John', // key:name, value:'John',
        age: 30, // key:age, value:30,
    }

프로퍼티 값엔 모든 자료형이 들어갈 수 있다.

## 프로퍼티의 삭제
    delete user.age;


## `in` 연산자로 프로퍼티 존재 여부 확인하기
    let user = {
        name: 'John',
        age: 30,
    };

    console.log('age' in user); // true(존재함)
    console.log('height' in user); // false(존재하지 않음)

프로퍼티는 따옴표로 감싸야한다.