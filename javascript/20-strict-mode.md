# 20-strict mode

### 20.1 strict mode란?

```javascript
// 20-01
function foo() {
    x = 10;
}
foo();

console.log(x);
```

* foo 함수 내에서 선언하지 않은 변숴에 값 10을 할당했다.
* 함수 스코프에도, 전역 스코프에도 x 변수의 선언이 존재하지 않기 때문에 ReferenceError를 발생시킬 것 같다.
* 하지만 자바스크립트 엔진은 암묵적으로 전역 객체에 x 프로퍼티를 동적 생성한다.
* 이때 전역 객체의 x 프로퍼티는 마치 전역 변수처럼 사용할 수 있다. 이러한 현상을 `암묵적 전역`이라 한다.
* 개발자의 의도와는 상관없이 발생한 암묵적 전역은 오류를 발생시키는 원인이 될 가능성이 크다. 따라서 반드시 변수 선언 키워드를 사용해야 한다.
* 하지만 오타나 문법 지식의 미비로 인한 실수는 언제나 발생한다.
* 이를 지원하기 위해 strict mode가 추가되었다. strict mode는 자바스크립트 언어의 문법을 좀 더 엄격히 적용하여 오류를 발생시킬 가능성이 높거나 자바스크립트 엔진의 최적화 작업에 문제를 일으킬 수 있는 코드에 대해 명시적인 에러를 발생시킨다.

### 20.2 strict mode의 적용

* 전역의 선두 또는 함수 몸체의 선두에 'use strict';를 추가한다.
* 전역의 선두에 추가하면 스크립트 전체에 적용된다.

```javascript
// 20-02
'use strict';

function foo() {
    x = 10; // ReferenceError: x is not defined
}
foo();
```

* 함수 몸체의 선두에 추가하면 해당 함수와 중첩 함수에 적용된다.

```javascript
// 20-03
function foo() {
    'use strict';

    x = 10; // ReferenceError: x is not defined
}
foo();
```

```javascript
// 20-04
function foo() {
    x = 10; // 에러를 발생시키지 않는다.
    'use strict';
}
foo();
```

### 20.3 전역에 strict mode를 적용하는 것은 피하자

* 전역에 적용한 strict mode는 스크립트 단위로 적용된다.

```javascript
// 20-05
<!DOCTYPE html>
<html>
    <body>
        <script>
            'use strict';
        </script>
        <script>
            x = 1; // 에러가 발생하지 않는다.
            console.log(x); // 1
        </script>
        <script>
            'use strict';
            y = 1; // ReferenceError: y is not defined
            console.log(y);
        </script>
    </body>
</html>
```

* strict mode 스크립트와 non-strict mode 스크립트를 혼용하는 것은 오류를 발생시킬 수 있다.
* 이러한 경우 즉시 실행 함수로 스크립트 전체를 감싸서 스코프를 구분하고 즉시 실행 함수의 선두에 strict mode를 적용한다.

```javascript
// 20-06
// 즉시 실행 함수의 선두에 strict mode 적용
(function() {
    'use strict';

    // Do something...
}());
```

### 20.4 함수 단위로 strict mode를 적용하는 것도 피하자

```javascript
// 20-07
(function() {
    // non-strict mode
    var let = 10; // 에러가 발생하지 않는다.
    
    function foo() {
        'use strict';

        let = 20; // SyntaxError: Unexpected strict mode reserved word
    }
    foo();
}());
```

* strict mode는 즉시 실행 함수로 감싼 스크립트 단위로 적용하는 것이 바람직하다.

### 20.5 strict mode가 발생시키는 에러

#### 20.5.1 암묵적 전역

```javascript
// 20-08
(function() {
    'use strict';

    x = 1;
    console.log(x); // ReferenceError: x is not defined
}());
```

#### 20.5.2 변수, 함수, 매개변수의 삭제

```javascript
// 20-09
(function() {
    'use strict';

    var x = 1;
    delete x; // SyntaxError: Delete of an unqualified identifier in strict mode.

    function foo(a) {
        delete a; // SyntaxError: Delete of an unqualified identifier in strict mode.
    }

    delete foo; // SyntaxError: Delete of an unqualified identifier in strict mode.
}());
```

#### 20.5.3 매개변수 이름의 중복

```javascript
// 20-10
(function () {
    'use strict';

    // SyntaxError: Duplicate parameter name not allowed in this context
    function foo(x, x) {
        return x + x;
    }
    console.log(foo(1, 2));
}());
```

#### 20.5.4 with 문의 사용

```javascript
// 20-11
(function() {
    'use strict';

    // SyntaxError: Strict mode code may not include a with statement
    with({ x: 1 }) {
        console.log(x);
    }
}());
```

### 20.6 strict mode 적용에 의한 변화

#### 20.6.1 일반 함수의 this

* strict mode에서 함수를 일반 함수로서 호출하면 this에 undefined가 바인딩된다.

```javascript
// 20-12
(function() {
    'use strict';

    function foo() {
        console.log(this); // undefined
    }
    foo();

    function Foo() {
        console.log(this); // Foo
    }
    new Foo();
}());
```

#### 20.6.2 arguments 객체

* strict mode에서는 매개변수에 전달된 인수를 재할당하여 변경해도 arguments 객체에 반영되지 않는다.

```javascript
// 20-13
(function (a) {
    'use strict';
    // 매개변수에 전달된 인수를 재할당하여 변경
    a = 2;

    // 변경된 인수가 arguments 객체에 반영되지 않는다.
    console.log(arguments); // [Arguments] { '0': 1, length: 1 }
}(1));
```

> Reference
>
> 모던 자바스크립트 DEEP DIVE
