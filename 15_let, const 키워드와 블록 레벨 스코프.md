## 15-1. var 키워드로 선언한 변수의 문제점

### 1) 변수 중복 선언 허용

같은 스코프 내에서 중복 선언이 허용된다.

초기화문이 있는 변수 선언문은 자바스크립트 엔진에 의해 var 키워드가 없는 것처럼 동작하고, 초기화문이 없는 선언은 무시된다. 이때 에러는 발생하지 않는다.

```jsx
var x = 1;
var y = 1;

// var 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용
var x = 100;
var y;

console.log(x); // 100
console.log(y); // 1
```

### 2) 함수 레벨 스코프

var 키워드는 함수 코드 블록만을 지역 스코프로 인정한다. 즉, 함수 외부에서 선언한 var 키워드는 모두 전역 변수가 된다.

```jsx
var x = 1;

if (true) {
  // x는 전역 변수다. 이미 선언된 전역 변수 x가 있으므로 x 변수는 중복 선언된다.
  var x = 10;
}

console.log(x); // 10
```

### 3) 변수 호이스팅

var로 선언한 변수는 변수 호이스팅에 의해 변수 선언문 이전에 참조할 수 있다. 단, 할당문 이전에 변수를 참조하면 undefined를 반환한다.

```jsx
// 이 시점에는 변수 호이스팅에 의해 이미 foo 변수가 선언(1. 선언 단계)
// 변수 foo는 undefined로 초기화 (2. 초기화 단계)
console.log(foo); // undefined

// 변수에 값을 할당(3. 할당 단계)
foo = 123;

console.log(foo); // 123

// 변수 선언은 런타임 이전에 자바스크립트 엔진에 의해 암묵적으로 실행
var foo;
```

---

## 15-2. let 키워드

### 1) 변수 중복 선언 금지

let 키워드로 변수 중복 선언 시 에러 발생

```jsx
let bar = 123;
let bar = 456; // SyntaxError: Identifier 'bar' has already been declared
```

### 2) 블록 레벨 스코프

let 키워드로 선언한 변수는 모든 코드 블록을 지역 스코프로 인정

```jsx
let foo = 1; // 전역 변수

{
  let foo = 2; // 지역 변수
  let bar = 3; // 지역 변수
}

console.log(foo); // 1
console.log(bar); // ReferenceError: bar is not defined
```

<img width="389" height="282" alt="image" src="https://github.com/user-attachments/assets/2a6bd90d-9a18-4053-b265-d654962c6420" />


### 3) 변수 호이스팅

변수 호이스팅이 발생하지 않는 것처럼 동작, 초기화 단계 전 변수에 접근하려고 하면 에러가 발생.

- **일시적 사각지대**: 스코프의 시작 지점부터 초기화 시작 지점까지의 변수를 참조할 수 없는 공간

```jsx
// 런타임 이전에 선언 단계가 실행된다. 아직 변수가 초기화되지 않았다.
// 초기화 이전의 일시적 사각 지대에서는 변수를 참조할 수 없다.
console.log(foo); // ReferenceError: foo is not defined

let foo; // 변수 선언문에서 초기화 단계가 실행된다.
console.log(foo); // undefined

foo = 1; // 할당문에서 할당 단계가 실행된다.
console.log(foo); // 1
```

let 키워드는 변수의 선언 단계, 초기화 단계가 분리되어 실행된다. 즉, 런타임 전 JS 엔진에 의해 선언 단계가 먼저 실행되지만 초기화 단계는 변수 선언문에 도달했을 때 실행

<img width="418" height="181" alt="image" src="https://github.com/user-attachments/assets/971ff1d4-658d-4787-98b5-28a23ee0b4c3" />


```jsx
let foo = 1; // 전역 변수

{
  console.log(foo); // ReferenceError: Cannot access 'foo' before initialization
  let foo = 2; // 지역 변수
}
```

let 키워드로 선언한 변수는 호이스팅이 되지 않는 것처럼 보이지만, 호이스팅이 되고 있다. 

호이스팅이 발생하지 않으면 위 예제는 전역변수 foo의 값을 출력해야 하지만 호이스팅이 발생하기 때문에 참조 에러가 발생한다.

 
>💡JS는 ES6에서 도입된 let, const를 포함해 모든 선언을 호이스팅한다. 하지만 ES6에서 도입된 let, const, class를 사용한 선언문은 호이스팅이 되지 않는 것처럼 동작한다.

 

### 4) 전역 객체와 let

let으로 선언한 전역 변수는 window의 프로퍼티가 아다.

```jsx
let x = 1;

// let, const 키워드로 선언한 전역 변수는 전역 객체 window의 프로퍼티가 아니다.
console.log(window.x); // undefined
console.log(x); // 1
```

---

## 15-3. const 키워드

상수를 선언하기 위해 사용하며 블록 레벨 스코프를 갖는다. 하지만 상수만을 위해 사용하지 않는다.

### 1) 선언과 초기화

선언과 동시에 초기화해야 한다.

```jsx
const foo = 1;
const str; // SyntaxError: Missing initializer in const declaration
```

### 2) 재할당 금지

```jsx
const foo = 1;
foo = 2; // TypeError: Assignment to constant variable.
```

### 3) 상수

const 키워드는 상수를 표현하는데 사용되기도 한다.

```jsx
// 변수 이름을 대문자로 선언해 상수임을 명확히 표현
const TAX_RATE = 0.1;

// 세전 가격
let preTaxPrice = 100;

// 세후 가격
let afterTaxPrice = preTaxPrice + (preTaxPrice * TAX_RATE);

console.log(afterTaxPrice); // 110
```

### 4) const 키워드와 객체

const 키워드로 선언된 변수에 객체를 할당하면 값을 변경할 수 있음.

**const는 재할당을 금지할 뿐 불변을 의미하지 않는다.**

```jsx
const person = {
  name: 'Lee'
};
person.name = 'Kim';

console.log(person); // {name: "Kim"}
```

---

## 15-4. var vs. let vs. const

변수 선언에는 기본적으로 const 사용, 재할당이 필요할 경우 let 사용. ES6을 사용한다면 var은 사용하지 않는다. 변수를 선언할 때는 일단 const 키워드를 사용하자.
