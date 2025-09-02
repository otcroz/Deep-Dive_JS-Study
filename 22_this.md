## 22-1. this 키워드

- 객체는 상태를 나타내는 **프로퍼티**, 동작을 나타내는 **메서드**를 하나의 논리적인 단위로 묶은 복합적인 자료구조
- 메서드는 프로퍼티를 참조하고 변경할 수 있어야 한다. 이때 메서드가 자신이 속한 객체의 프로퍼티를 참조하려면 먼저 **자신이 속한 객체를 가리키는 식별자를 참조할 수 있어야 한다.**

```jsx
const circle = {
  // 프로퍼티: 객체 고유의 상태 데이터
  radius: 5, 
  // 메서드: 상태 데이터를 참조하고 조작하는 동작
  getDiameter() {
    // 이 메서드가 자신이 속한 객체의 프로퍼티나 다른 메서드를 참조하려면
    // 자신이 속한 객체인 circle을 참조할 수 있어야 한다.
    return 2 * circle.radius;
  }
};
 
console.log(circle.getDiameter()); // 10
```

- 객체 리터럴 방식: 메서드 내부에서 메서드 자신이 속한 객체를 가리키는 식별자를 재귀적으로 참조
- 생성자 함수: 객체를 동적으로 생성, 자신이 속한 객체를 가리키는 식별자를 사전에 알 수 없다.
    
    ⇒ 이를 위해 this를 제공. 
    
     
    >❓
    >
    >**this**는 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 **자기 참조 변수(self-referencing variable)**이다. 
    >
    >자신이 속한 객체 또는 자신이 생성할 인스턴스의 프로퍼티나 메서드를 참조하는 데 사용된다.
    
     
    

```jsx
// this는 어디서든지 참조 가능하다.
// 전역에서 this는 전역 객체 window를 가리킨다.
console.log(this); // window
 
function square(number) {
  // 일반 함수 내부에서 this는 전역 객체 window를 가리킨다.
  console.log(this); // window
  return number * number;
}
square(2);
 
const person = {
  name: 'Lee',
  getName() {
    // 메서드 내부에서 this는 메서드를 호출한 객체를 가리킨다.
    console.log(this); // {name: "Lee", getName: ƒ}
    return this.name;
  }
};
console.log(person.getName()); // Lee
 
function Person(name) {
  this.name = name;
  // 생성자 함수 내부에서 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  console.log(this); // Person {name: "Lee"}
}
 
const me = new Person('Lee');
```

- this 바인딩은 함수 호출 방식에 의해 **동적으로 결정**
- this는 코드 전역, 함수 내부 등 어디서든지 참조 가능
- strict mode가 적용된 일반 함수 내부의 this에는 undefined가 바인딩. 일반 함수 내부에서는 this를 사용할 필요가 없기 때문

## 22-2. 함수 호출 방식과 this 바인딩

함수 호출 방식에 따라 this 바인딩이 동적으로 결정된다.

- 일반 함수 호출
- 메서드 호출
- 생성자 함수 호출
- Function.prototype.apply/call/bind 메서드에 의한 간접 호출

```jsx
Function.prototype.apply/call/bind 메서드에 의한 간접 호출
// this 바인딩은 함수 호출 방식에 따라 동적으로 결정된다.
const foo = function () {
  console.dir(this);
};
 
// 동일한 함수도 다양한 방식으로 호출할 수 있다.
 
// 1. 일반 함수 호출
// foo 함수 내부의 this는 전역 객체 window를 가리킨다.
foo(); // window
 
// 2. 메서드 호출 
// foo 함수 내부의 this는 메서드를 호출한 객체 obj를 가리킨다.
const obj = { foo };
obj.foo(); // obj
 
// 3. 생성자 함수 호출
// foo 함수 내부의 this는 생성자 함수가 생성한 인스턴스를 가리킨다.
new foo(); // foo {}
 
// 4. Function.prototype.apply/call/bind 메서드에 의한 간접 호출
// foo 함수 내부의 this는 인수에 의해 결정된다.
const bar = { name: 'bar' };
 
foo.call(bar);   // bar
foo.apply(bar);  // bar
foo.bind(bar)(); // bar
```

- **렉시컬 스코프**: 함수 정의가 평가되어 함수 객체가 생성되는 시점에 상위 스코프 결정
- **this 바인딩**: 함수 호출 시점에 결정

### 1) 일반 함수 호출

this에는 **전역 객체**가 바인딩

```jsx
function foo() {
  console.log("foo's this: ", this);  // window
  function bar() {
    console.log("bar's this: ", this); // window
  }
  bar();
}
foo();
```

중첩 함수를 일반 함수로 호출하는 경우도 **전역 객체** 바인딩

```jsx
function foo() {
  'use strict';
 
  console.log("foo's this: ", this);  // undefined
  function bar() {
    console.log("bar's this: ", this); // undefined
  }
  bar();
}
foo();
```

객체를 생성하지 않는 일반 함수에서 this는 의미가 없으므로, **strict mode**가 적용된 일반 함수 내부의 this에는 **undefined**가 바인딩.

콜백 함수가 일반 함수로 호출되면 콜백 함수 내 this에도 전역 객체가 바인딩.

```jsx
var value = 1;
 
const obj = {
  value: 100,
  foo() {
    console.log("foo's this: ", this); // {value: 100, foo: ƒ}
    // 콜백 함수 내부의 this에는 전역 객체가 바인딩된다.
    setTimeout(function () {
      console.log("callback's this: ", this); // window
      console.log("callback's this.value: ", this.value); // 1
    }, 100);
  }
};
 
obj.foo();
```

즉, 일반 함수로 호출된 모든 함수(중첩 함수, 콜백 함수 포함) 내부의 this에는 **전역 객체가 바인딩**

```jsx
var value = 1;
 
const obj = {
  value: 100,
  foo() {
    // this 바인딩(obj)을 변수 that에 할당한다.
    const that = this;
 
    // 콜백 함수 내부에서 this 대신 that을 참조한다.
    setTimeout(function () {
      console.log(that.value); // 100
    }, 100);
  }
};
 
obj.foo();
var value = 1;
 
const obj = {
  value: 100,
  foo() {
    // 콜백 함수에 명시적으로 this를 바인딩한다.
    setTimeout(function () {
      console.log(this.value); // 100
    }.bind(this), 100);
  }
};
 
obj.foo();
var value = 1;
 
const obj = {
  value: 100,
  foo() {
    // 화살표 함수 내부의 this는 상위 스코프의 this를 가리킨다.
    setTimeout(() => console.log(this.value), 100); // 100
  }
};
 
obj.foo();
```

### 2) 메서드 호출

- 메서드 내부의 this에는 메서드를 호출한 객체(=메서드를 호출할 때 메서드 이름 앞의 마침표(.) 연산자 앞에 기술한 객체)가 바인딩
- 메서드 내부의 this는 **메서드를 소유한 객체가 아닌 메서드를 호출한 객체에 바인딩**

```jsx
const person = {
  name: 'Lee',
  getName() {
    // 메서드 내부의 this는 메서드를 호출한 객체에 바인딩된다.
    return this.name;
  }
};
 
// 메서드 getName을 호출한 객체는 person이다.
console.log(person.getName()); // Lee
```

다른 **객체의 프로퍼티**에 할당하는 것으로 다른 객체의 메서드가 될 수 있고 **일반 변수에 할당**하여 일반 함수로 호출될 수도 있다.

```jsx
const anotherPerson = {
  name: 'Kim'
};
// getName 메서드를 anotherPerson 객체의 메서드로 할당
anotherPerson.getName = person.getName;
 
// getName 메서드를 호출한 객체는 anotherPerson이다.
console.log(anotherPerson.getName()); // Kim
 
// getName 메서드를 변수에 할당
const getName = person.getName;
 
// getName 메서드를 일반 함수로 호출
console.log(getName()); // ''
// 브라우저 환경에서 window.name은 브라우저 창의 이름을 나타내는 빌트인 프로퍼티이며 기본값은 ''이다.
```

프로토타입 메서드 내부에서 사용된 this도 일반 메서드와 마찬가지로 **해당 메서드를 호출한 객체에 바인딩**

- ①의 경우, getName 메서드를 호출한 객체는 me이므로 getName 메서드 내부의 this는 me를 가리키고 this.name은 'Lee'다.
- ②의 경우, getName 메서드를 호출한 객체는 Person.prototype이다. Person.prototype도 객체이므로 직접 메서드를 호출할 수 있다. 따라서 getName 메서드 내부의 this는 Person.prototype을 가리키고 this.name은 'Kim'이다.

```jsx
function Person(name) {
  this.name = name;
}
 
Person.prototype.getName = function () {
  return this.name;
};
 
const me = new Person('Lee');
 
// getName 메서드를 호출한 객체는 me다.
console.log(me.getName()); // ① Lee
 
Person.prototype.name = 'Kim';
 
// getName 메서드를 호출한 객체는 Person.prototype이다.
console.log(Person.prototype.getName()); // ② Kim
```

### 3) 생성자 함수 호출

- 생성자 함수 내부의 this에는 생성자 함수가 (미래에) 생성할 인스턴스가 바인딩
- 생성자 함수는 새로운 객체를 생성하는 역할을 하며, 내부의 this는 이 새로 생성될 객체를 참조

```jsx
// 생성자 함수
function Circle(radius) {
  // 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}
 
// 인스턴스 생성
const circle1 = new Circle(5);  // 반지름이 5인 Circle 객체를 생성
const circle2 = new Circle(10); // 반지름이 10인 Circle 객체를 생성
 
console.log(circle1.getDiameter()); // 10
console.log(circle2.getDiameter()); // 20
```

this.radius는 해당 인스턴스의 radius 프로퍼티에 값을 할당하고, this.getDiameter는 해당 인스턴스의 메서드로 생성

생성자 함수는 일반 함수와 동일한 방법으로 정의, new 연산자와 함께 호출하면 해당 함수는 생성자 함수로 동작한다. 하지만 new 연산자 없이 호출하면 일반 함수로 동작한다.

```jsx
const circle3 = Circle(15); // new 연산자 없이 일반 함수로 호출
 
console.log(circle3); // undefined
 
console.log(radius); // 15
```

- 일반 함수 내부의 this는 전역 객체 **window**를 가리키므로 this.radius는 **window.radius**와 같고, radius 값이 전역 변수처럼 사용되어 값이 할당
- Circle 함수에는 별도의 반환문이 없으므로 일반 함수로써 호출된 Circle 함수는 undefined를 반환 ⇒ 생성자 함수는 new 연산자와 함께 호출해야 한다.

### **4) Function.prototype.apply/call/bind 메서드에 의한 간접 호출**

apply, call, bind 메서드는 Function.prototype의 메서드로서 모든 함수가 상속받아 사용

### **■ apply와 call 메서드**

함수 호출, 함수를 호출하면서 첫 번째 인수로 전달한 특정 객체를 호출한 함수의 this에 바인딩한다. 즉, **this로 사용할 객체를 첫 번째 인수로 전달 + 함수 호출**

```jsx
function getThisBinding() {
  return this;
}
 
// this로 사용할 객체
const thisArg = { a: 1 };
 
// apply 메서드는 호출할 함수의 인수를 배열로 묶어 전달
console.log(getThisBinding.apply(thisArg, [1, 2, 3]));
// Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
// {a: 1}
 
// call 메서드는 호출할 함수의 인수를 쉼표로 구분한 리스트 형식으로 전달
console.log(getThisBinding.call(thisArg, 1, 2, 3));
// Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
// {a: 1}
```

- 호출할 함수에 **인수를 전달하는 방식만 다를 뿐** this로 사용할 객체를 전달하면서 함수를 호출하는 것은 동일

```jsx
function convertArgsToArray() {
  console.log(arguments);
 
  // arguments 객체를 배열로 변환
  // Array.prototype.slice를 인수없이 호출하면 배열의 복사본을 생성한다.
  const arr = Array.prototype.slice.call(arguments);
  // const arr = Array.prototype.slice.apply(arguments);
  
  console.log(arr);
  return arr;
}
 
convertArgsToArray(1, 2, 3); // [1, 2, 3]
```

- apply와 call 메서드의 대표적인 용도: arguments 등 같은 유사 배열 객체에 배열 메서드를 사용하는 경우로, arguments 객체는 배열이 아니기 때문에 **Array.prototype.slice**와 같은 배열의 메서드를 사용할 수 없는데, apply나 call 메서드를 이용하면 가능

### ■ bind 메서드

 함수를 호출하지 않고 this로 사용할 객체만 전달,  bind 메서드는 **메서드의 this**와 **메서드 내부의 중첩 함수 또는 콜백 함수의 this**가 불일치하는 문제를 해결하기 위해 사용

```jsx
function getThisBinding() {
  return this;
}
 
// this로 사용할 객체
const thisArg = { a: 1 };
 
// bind 메서드는 함수에 this로 사용할 객체를 전달한다.
// bind 메서드는 함수를 호출하지는 않는다.
console.log(getThisBinding.bind(thisArg)); // getThisBinding
 
// bind 메서드는 함수를 호출하지는 않으므로 명시적으로 호출해야 한다. 
console.log(getThisBinding.bind(thisArg)()); // {a: 1}
```

**전역에서 호출된 일반 함수로 취급되어 this 바인딩이 전역 객체로 변경되는 문제**

person.foo 메서드가 호출되기 직전인 1번 시점에서 this는 **person** 객체를 가리킨다. 그런데 메서드 내부에서 일반 함수(콜백함수)를 호출하면 그 함수 내부에서 this 바인딩이 다시 변경된다. 일반 함수로 호출된 콜백 함수 내부의 **2번 시점에서 this는 전역 객체 window를 가리키게 된다.**

⇒ 콜백 함수는 외부 함수인 메서드 내부의 this와 별개로, **전역에서 호출된 일반 함수로 취급되어 this 바인딩이 전역 객체로 변경되는 문제**

```jsx
const person = {
  name: 'Lee',
  foo(callback) {
    // 1. 호출되기 직전 시점
    setTimeout(callback, 100);
  }
};
 
person.foo(function () {
  console.log(`Hi! my name is ${this.name}.`); // 2. Hi! my name is .
});
```

bind 메서드를 사용하여 콜백 함수 내부의 this를 전달하면, 외부 함수 내부의 this와 콜백 함수 내부의 this가 동일한 객체를 가리키게 된다.

```jsx
const person = {
  name: 'Lee',
  foo(callback) {
    // bind 메서드로 callback 함수 내부의 this 바인딩을 전달
    setTimeout(callback.bind(this), 100);
  }
};
 
person.foo(function () {
  console.log(`Hi! my name is ${this.name}.`); // Hi! my name is Lee.
});
```
