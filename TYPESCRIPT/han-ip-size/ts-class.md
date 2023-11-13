# 클래스

## 자바스크립트의 클래스

```js
class Student {
  // Fields
  name;
  age;
  grade;

  // Constructor
  constructor(name, grade, age) {
    this.name = name;
    this.grade = grade;
    this.age = age;
  }

  // Methods
  study() {
    console.log(`${this.name}은 공부 중`);
  }

  introduce() {
    console.log(`Hi, My name is ${this.name}.`);
  }
}

const studentA = new Student('박성현', 'A+', 26);

console.log(studentA); // Student { name: '박성현', age: 26, grade: 'A+' }
```

<br>

## 타입스크립트의 클래스

TS에서는 클래스의 필드를 선언할 때 타입 주석으로 타입을 함께 정의해줘야 한다.

그렇지 않으면 함수 파라미터와 동일하게 암시적 any 타입으로 추론되는데 `strict: true`일 경우 오류가 발생하게 됨

<br>

추가로 생성자에서 각 필드의 값을 초기화하지 않을 경우 초깃값도 함께 명시해줘야 함

```ts
class Employee {
  // fields
  name: string = '';
  age: number = 0;
  position: string = '';

  // methods
  work() {
    console.log('일중');
  }
}
```

만약 다음과 같이 생성자 함수에서 필드의 값들을 잘 초기화 해준다면 필드 선언시의 초깃값은 생략해도 된다

<br>

```ts
class Employee {
  // fields
  name: string = "";
  age: number = 0;
  position?: string = "";

  // constructor
  constructor(name: string, age: number, position: string) {
    this.name = name;
    this.age = age;
    this.position = position;
  }

  // methods
  work() {
    console.log("일함");
  }
}
```

<br>

<h3>클래스는 타입이다.</h3>

TS으 클래스는 타입으로도 사용할 수 있다. 클래스르 타입으로 사용하면 해당 클래스가 생성하는 객체의 타입과 동일한 타입이 된다.

```ts
class Employee {
  (...)
}

const employeeC: Employee = {
  name: '',
  age: 0,
  position: '',
  work() {},
}
```

변수 `employeeC`으 타입을 Employee 클래스로 정의했다. 따라서 이 변수는 name, age, position 필드와 work 메서드를 갖는 객체 타입으로 정의된다.

<br>

<h3>상속</h3>

TS에서 클래스의 상속을 이용할 때 자식 클래스에서 생성자를 정의했다면 반드시 `super`를 사용해 부모 클래스의 생성자를 호출해야 하며, 호출 위치는 생성자의 최상단 **이어야만 한다.**

<br>

```ts
class ExecutiveOfficer extends Employee {
  officeNumber: number,

  constructor (
    name: string,
    age: number,
    position: string,
    officeNumber: number
  ) {
    super(name, age, position); // 생성자 최상단에 부모 생성자 호출
    this.officeNumber = officeNumber;
  }
}
```

<br>

## 접근 제어자

자바랑 99% 정도 같음

<br>

- public: 모든 범위에서 접근 가능. 따로 명시하지 않으면 기본 값
- private: 클래스 내부에서만 접근 가능
- protected: 클래스 내부 또는 자식 클래스 내부에서만 접근 가능. (상속만 허용)

<br>

<h3>필드 생략하기</h3>

접근 제어자는 다음과 같이 생성자의 매개 변수에도 설정할 수 있다.

```ts
class Employee {
  // fields
  private name: string; // ❌
  protected age: number; // ❌
  public position: string; // ❌

  // constructor
  constructor (
    private name: string,
    protected age: number,
    public position: string,
  ) {
    this.name = name;
    this.age = age;
    this.position = position;
  }

  // methods
  work() {
    console.log(`${this.name} 일한다`);
  }
}
```

생성자에 접근 제어자를 걸어줄 경우 동일한 이름의 필드는 선언할 수 없게 된다.<br>

**이유**: 생성자 매개 변수에 접근 제어자가 설정되면 자동으로 필드도 함께 선언되기 때문

근데 이렇게 써야하나 ...

<br>

## 인터페이스와 클래스

### 인터페이스를 구현하는 클래스

TS의 인터페이스는 클래스의 설계도 역할을 할 수 있다.

쉽게 말해 다음과 같이 인터페이스를 이용하면 클래스에 어떤 필드들이 존재하고, 어떤 메서드가 존재하는지 정의할 수 있다.

```ts
interface CharacterInterface {
  name: string,
  moveSpeed: number,
  move(): void,
}

class Character implements CharacterInterface {
  constructor(
    public name: string,
    public moveSpeed: number,
    private extra: string,
  ) {}

  move(): void {
    console.log(`현재 속력 ${this.moveSpeed}km/h`)
  }
}
```

구현 강제시키는 용도