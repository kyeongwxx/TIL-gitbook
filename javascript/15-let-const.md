# 15-let, const 키워드와 블록 레벨 스코프

### 15.1 var 키워드로 선언한 변수의 문제점

#### 15.1.1 변수 중복 선언 허용

```javascript
// 15-01
var x = 1;
var y = 1;
jj
// var 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용한다.
// 초기화문이 있는 변수 선언문은 자바스크립트 엔진에 의해 var 키워드가 없는 것처럼 동작한다.
var x = 100;
// 초기화문이 없는 변수 선언문은 무시된다.
var y;

console.log(x); // 100
console.log(y); // 1
```

#### 15.1.2 함수 레벨 스코프

* var 키워드로 선언한 변수는 오로지 함수의 코드 블록만을 지역 스코프로 인정
* 따라서 함수 외부에서 var 키워드로 선언한 변수는 코드 블록 내에서 선언해도 모두 전역 변수가 된다.

```javascript
// 15-02
var x = 1;

if(true) {
    // x는 전역 변수다. 이미 선언된 전역 변수 x가 있으므로 x 변수는 중복 선언된다.
    // 이는 의도치 않게 변수값이 변경되는 부작용을 발생시킨다.
    var x = 10;
}

console.log(x); // 10
```

```javascript
// 15-03
var i = 10;

// for문에서 선언한 i는 전역 변수다. 이미 선언된 전역 변수 i가 있으므로 중복 선언된다.
for (var i = 0; i < 5; i++) {
    console.log(i) // 0 1 2 3 4
}

// 의도치 않게 i 변수의 값이 변경되었다.
console.log(i); // 5
```

#### 15.1.3 변수 호이스팅

* 변수 선언문 이전에 변수를 참조하는 것은 변수 호이스팅에 의해 에러를 발생시키지는 않지만 프로그램의 흐름상 맞지 않을뿐더러 가독성을 떨어뜨리고 오류를 발생시킬 여지를 남긴다.

```javascript
// 15-04
// 이 시점에는 변수 호이스팅에 의해 이미 foo 변수가 선언되었다.(1. 선언 단계)
// 변수 foo는 undefined로 초기화된다.(2. 초기화 단계)
console.log(foo);

// 변수에 값을 할당(3. 할당 단계)
foo = 123;

console.log(123); // 123

// 변수 선언은 런타임 이전에 자바스크립트 엔진에 의해 암묵적으로 실행된다.
var foo;
```

### 15.2 let 키워드

#### 15.2.1 변수 중복 선언 금지

```javascript
// 15-05
var foo = 123;
// var 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용한다.
// 아래 변수 선언문은 자바스크립트 엔진에 의해 var 키워드가 없는 것처럼 동작한다.

var foo = 456;

let bar = 123;
// let이나 const 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용하지 않는다.
let bar = 456; // SyntaxError: Identifier 'bar' has already been declared
```

#### 15.2.2 블록 레벨 스코프

* let 키워드로 선언한 변수는 모든 코드 블록(함수, if 문, for 문, while 문, try...catch 문 등)을 지역 스코프로 인정하는 블록 레벨 스코프를 따른다.

```javascript
// 15-06
let foo = 1; // 전역 변수

{
    let foo = 2; // 지역 변수
    let bar = 3; // 지역 변수
}

console.log(foo); // 1
console.log(bar); // ReferenceError: bar is not defined
```

#### 15.2.3 변수 호이스팅

* let 키워드로 선언한 변수는 변수 호이스팅이 발생하지 않는 것처럼 동작한다.

```javascript
// 15-07
console.log(foo); // ReferenceError: foo is not defined
let foo;
```

* var 키워드로 선언한 변수는 런타임 이전에 "선언 단계"와 "초기화 단계"가 한번에 진행된다.

```javascript
// 15-08
// var 키워드로 선언한 변수는 런타임 이전에 선언 단계와 초기화 단계가 실행된다.
// 따라서 변수 선언문 이전에 변수를 참조할 수 있다.
console.log(foo); // undefined

var foo;
console.log(foo); // undefined

foo = 1; // 할당문에서 할당 단계가 실행된다.
console.log(foo); // 1
```

* `let 키워드로 선언한 변수는 "선언 단계"와 "초기화 단계"가 분리되어 진행된다.` 즉, 런타임 이전에 자바스크립트 엔진에 의해 암묵적으로 선언 단계가 먼저 실행되지만 초기화 단계는 변수 선언문에 도달했을 때 실행된다.
* let 키워드로 선언한 변수는 스코프의 시작 지점부터 초기화 단계 시작 지점(변수 선언문)까지 변수를 참조할 수 없다. 이 구간을 `일시적 사각지대`라고 부른다.

```javascript
// 15-09
// 런타임 이전에 선언 단계가 실행된다. 아직 변수가 초기화되지 않았다.
// 초기화 이전의 일시적 사각지대에서는 변수를 참조할 수 없다.
console.log(foo); // ReferenceError: foo is not defined

let foo; // 변수 선언문에서 초기화 단계가 실행된다.
console.log(foo); // undefined

foo = 1; // 할당문에서 할당 단계가 실행된다.
console.log(foo); // 1
```

* 결국 let 키워드로 선언한 변수는 변수 호이스팅이 발생하지 않는 것처럼 보이지만 그렇지 않다.

```javascript
// 15-10
let foo = 1; // 전역 변수

{
    console.log(foo); // ReferenceError: Cannot access 'foo' before initialization
    let foo = 2; // 지역 변수 
}
```

* let 키워드로 선언한 변수의 경우 변수 호이스팅이 발생하지 않는다면 위 예제는 전역 변수 foo의 값을 출력해야 한다. 하지만 let 키워드로 선언한 변수도 여전히 호이스팅이 발생하기 때문에 참조 에러가 발생한다.
* 즉 let, const를 포함한 모든 선언은 호이스팅되지만, let, const, class를 사용한 선언문은 호이스팅이 발생하지 않는 것처럼 동작한다.

#### 15.2.4 전역 객체와 let

* var 키워드로 선언한 전역 변수와 전역 함수, 그리고 선언하지 않은 변수에 값을 할당한 암묵적 전역은 전역 객체 window의 프로퍼티가 된다. 전역 객체의 프로퍼티를 참조할 때 window를 생략할 수 있다.

```javascript
// 15-11
// 전역 변수
var x = 1;
// 암묵적 전역
y = 2;
// 전역 함수
function foo() {}

// var 키워드로 선언한 전역 변수는 전역 객체 window의 프로퍼티다.
console.log(window.x); // 1
// 전역 객체 window의 프로퍼티는 전역 변수처럼 사용할 수 있다.
console.log(x); // 1

// 암묵적 전역은 전역 객체 window의 프로퍼티다.
console.log(window.y); // 2
console.log(y); // 2

// 함수 선언문으로 정의한 전역 변수는 전역 객체 window의 프로퍼티다.
console.log(window.foo); // f foo() {}
// 전역 객체 window의 프로퍼티는 전역 변수처럼 사용할 수 있다.
console.log(foo); // f foo() {}
```

* let 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 아니다. 즉, window.foo와 같이 접근할 수 없다.
* let 전역 변수는 보이지 않는 개념적인 블록(전역 렉시컬 환경의 선언적 환경 레코드) 내에 존재하게 된다.

```javascript
// 15-12
let x = 1;

// let, const 키워드로 선언한 전역 변수는 전역 객체 window의 프로퍼티가 아니다.
console.log(window.x); // undefined
console.log(x); // 1
```

### 15.3 const 키워드

* const 키워드는 상수를 선언하기 위해 사용한다.

#### 15.3.1 선언과 초기화

* `const 키워드로 선언한 변수는 반드시 선언과 동시에 초기화해야 한다.`

```javascript
// 15-13
const foo = 1;
```

```javascript
// 15-14
const foo; // SyntaxError: Missing initializer in const declaration
```

* const 키워드로 선언한 변수는 블록 레벨 스코프를 가지며, 변수 호이스팅이 발생하지 않는 것처럼 동작한다.

```javascript
// 15-15
{
    // 변수 호이스팅이 발생하지 않는 것처럼 동작한다.
    console.log(foo); // ReferenceError: Cannot access 'foo' before initialization
    const foo = 1;
    console.log(foo); // 1
}

// 블록 레벨 스코프를 갖는다.
console.log(foo); // ReferenceError: foo is not defined
```

#### 15.3.2 재할당 금지

* `const 키워드로 선언한 변수는 재할당이 금지된다.`

```javascript
// 15-16
const foo = 1;
foo = 2; // TypeError: Assignment to constant variable.
```

#### 15.3.3 상수

* `const 키워드로 선언한 변수에 원시 값을 할당한 경우 변수 값을 변경할 수 없고, 재할당할 수 없으므로 변경할 수 있는 방법이 없다.`

```javascript
// 15-17
// 세전 가격
let preTaxPrice = 100;

// 세후 가격
// 0.1의 의미를 명확히 알기 어렵기 때문이 가독성이 좋지 않다.
let afterTaxPrice = preTaxPrice + (preTaxPrice * 0.1);

console.log(afterTaxPrice); // 110
```

```javascript
// 15-18
// 세율을 의미하는 0.1은 변경할 수 없는 상수로서 사용될 값이다.
// 변수 이름을 대문자로 선언해 상수임을 명확히 나타낸다.
const TAX_RATE = 0.1;

// 세전 가격
let preTaxPrice = 100;

// 세후 가격
let afterTaxPrice = preTaxPrice + (preTaxPrice * TAX_RATE);

console.log(afterTaxPrice); // 110
```

#### 15.3.4 const 키워드와 객체

* `const 키워드로 선언된 변수에 객체를 할당한 경우 값을 변경할 수 있다.`

```javascript
// 15-19
const person = {
    name: 'Lee'
};

// 객체는 변경 가능한 값이다. 따라서 재할당 없이 변경이 가능하다.
person.name = 'Kim';

console.log(person); // { name: "Kim" }
```

* `const 키워드는 재할당을 금지할 뿐 "불변"을 의미하지는 않는다.` 다시 말해, 새로운 값을 재할당하는 것은 불가능하지만 프로퍼티 동적 생성, 삭제, 프로퍼티 값의 변경을 통해 객체를 변경하는 것은 가능하다.

### 15.4 var vs let vs const

* ES6를 사용한다면 var 키워드는 사용하지 않는다.
* 재할당이 필요한 경우에 한정해 let 키워드를 사용한다. 이때 변수의 스코프는 최대한 좁게 만든다.
* 변경이 발생하지 않고 읽기 전용으로 사용하는(재할당이 필요 없는 상수) 원시 값과 객체에는 const 키워드를 사용한다.
