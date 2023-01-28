---
description: 표준 빌트인 객체인 String은 원시 타입인 문자열을 다룰 때 유용한 프로퍼티와 메서드를 제공한다.
---

# 32-String

## 32.1 String 생성자 함수

표준 빌트인 객체인 String 객체는 생성자 함수 객체다. 따라서 new 연산자와 함께 호출하여  String 인스턴스를 생성할 수 있다.

인수를 전달하지 않고 new 연산자와 함께 호출하면 \[\[StringData]] 내부 슬롯에 빈 문자열을 할당한 String 래퍼 객체를 생성한다.

```javascript
const strObj = new String();
console.log(strObj); // String {length: 0, [[PrimitiveValue]]: ""}
```

문자열을 인수로 전달하면서 new 연산자와 함께 호출하면 \[\[StringData]] 내부 슬롯에 인수로 전달받은 문자열을 할당한 String 래퍼 객체를 생성한다.

```javascript
const strObj = new String('Lee');
console.log(strObj);
// String {0: "L", 1: "e", 2, "e", length: 3, [[PrimitiveValue]]: "Lee"}
```

String 래퍼 객체는 배열과 마찬가지로 length 프로퍼티와 인덱스를 나타내는 숫자 형식의 문자열을 프로퍼티 키로, 각 문자를 프로퍼티 값으로 갖는 유사 배열 객체이면서 이터러블이다.

따라서 인덱스를 사용하여 각 문자에 접근할 수 있다.

```javascript
const strObj = new String('Lee');
console.log(strObj[0]); // L
```

단, 문자열은 원시 값이므로 변경할 수 없다.

```javascript
// 문자열은 원시 값이므로 변경할 수 없다.
const strObj = new String('Lee');

strObj[0] = 'S';
console.log(strObj); // 'Lee'
```

String 생성자 함수의 인수로 문자열이 아닌 값을 전달하면 인수를 문자열로 강제 변환한 후, \[\[StringData]] 내부 슬롯에 변환된 문자열을 할당한 String 래퍼 객체를 생성한다.

```javascript
let strObj = new String(123);
console.log(strObj);
// String {0: "1", 1: "2", 2: "3", length: 3, [[PrimitiveValue]]: "123"}

strObj = new String(null);
console.log(strObj);
// String {0: "n", 1: "u", 2: "l", 3: "l" length: 4, [[PrimitiveValue]]: "null"}
```

new 연산자를 사용하지 않고 String 생성자 함수를 호출하면 String 인스턴스가 아닌 문자열을 반환한다. 이를 이용하여 명시적으로 타입을 변환하기도 한다.

```javascript
// 숫자 타입 -> 문자열 타입
String(1);        // '1'
String(NaN);      // 'NaN'
String(Infinity); // 'Infinity'

// 불리언 타입 -> 문자열 타입
String(true);     // 'true'
String(false);    // 'false'
```

## 32.2 length 프로퍼티

length 프로퍼티는 문자열의 문자 개수를 반환한다.

```javascript
'Hello'.length;    // 5
'안녕하세요!'.length; // 6
```

String 래퍼 객체는 배열과 마찬가지로 length 프로퍼티를 갖는다. 그리고 인덱스를 나타내는 숫자를 프로퍼티 키로, 각 문자를 프로퍼티 값으로 가지므로 String 래퍼 객체는 유사 배열 객체다.

## 32.3 String 메서드

String 객체의 메서드는 언제나 새로운 문자열을 반환한다. 문자열은 변경 불가능한 원시 값이기 때문에 <mark style="color:purple;">**String 래퍼 객체도 읽기 전용(read only) 객체로 제공**</mark>된다.

```javascript
const strObj = new String('Lee');

console.log(Object.getOwnPropertyDescriptors(strObj));
/* String 래퍼 객체는 읽기 전용 객체다. 즉, writable 프로퍼티 어트리뷰트 값이 false다.
{
  0: {value: 'L', writable: false, enumerable: true, configurable: false},
  1: {value: 'e', writable: false, enumerable: true, configurable: false},
  2: {value: 'e', writable: false, enumerable: true, configurable: false},
  length: {value: 3, writable: false, enumerable: false, configurable: false}
}
*/
```

### 32.3.1 String.prototype.indexOf

인수로 전달받은 문자열을 검색하여 첫 번째 인덱스를 반환한다. 실패 시 -1 반환.

```javascript
const str = 'Hello World';

// 문자열 str에서 'l'을 검색하여 첫 번째 인덱스를 반환한다.
str.indexOf('l'); // 2

// 문자열 str에서 'or'을 검색하여 첫 번째 인덱스를 반환한다.
str.indexOf('or'); // 7

// 문자열 str에서 'x'을 검색하여 첫 번째 인덱스를 반환한다. 검색에 실패하면 -1을 반환한다.
str.indexOf('x'); // -1
```

2번째 인수로 검색을 시작할 인덱스를 전달할 수 있다.

```javascript
// 문자열 str의 인덱스 3부터 'l'을 검색하여 첫 번째 인덱스를 반환한다.
const str = 'Hello World';

str.indexOf('l', 3); // 3
```

특정 문자열이 존재하는지 확인할 때 유용하다.

```javascript
const str = 'Hello World';

if(str.indexOf('Hello') !== -1) {
    // 문자열 str에 'Hello'가 포함되어 있는 경우에 처리할 내용
}
```

includes 메서드를 사용하면 가독성이 더 좋다.

```javascript
if(str.includes('Hello')) {
    // 문자열 str에 'Hello'가 포함되어 있는 경우에 처리할 내용
}
```

### 32.3.2 String.prototype.search

인수로 전달받은 정규 표현식과 매치하는 문자열을 검색하여 일치하는 문자열의 인덱스를 반환한다.

실패 시 -1 반환.

```javascript
const str = 'Hello World';

// 문자열 str에서 정규 표현식과 매치하는 문자열을 검색하여 일치하는 문자열의 인덱스를 반환한다.
str.search(/o/); // 4
str.search(/x/); // -1
```

### 32.3.3 String.prototype.includes

인수로 전달받은 문자열이 포함되어 있는지 확인하여 그 결과를 true 또는 false로 반환한다.

```javascript
const str = 'Hello World';

str.includes('Hello'); // true
str.includes('');      // true
str.includes('x');     // false
str.includes();        // false
```

2번째 인수로 검색을 시작할 인덱스를 전달할 수 있다.

```javascript
const str = 'Hello World';

// 문자열 str의 인덱스 3부터 'l'이 포함되어 있는지 확인
str.includes('l', 3); // true
str.includes('H', 3); // false
```

### 32.2.4 String.prototype.startsWith

인수로 전달받은 문자열로 시작하는지 확인하여 그 결과를 true 또는 false로 반환한다.

```javascript
const str = 'Hello World';

// 문자열 str이 'He'로 시작하는지 확인
str.startsWith('He'); // true
// 문자열 str이 'x'로 시작하는지 확인
str.startsWith('x');  // false
```

2번째 인수로 검색을 시작할 인덱스를 전달할 수 있다.

```javascript
const str = 'Hello World';

// 문자열 str의 인덱스 5부터 시작하는 문자열이 ' '로 시작하는지 확인
str.startsWith(' ', 5); // true
```

### 32.2.5 String.prototype.endsWith

인수로 전달받은 문자열로 끝나는지 확인하여 그 결과를 true 또는 false로 반환한다.

```javascript
const str = 'Hello World';

// 문자열 str이 'ld'로 끝나는지 확인
str.endsWith('ld'); // true
// 문자열 str이 'x'로 끝나는지 확인
str.endsWith('x');  // false
```

2번째 인수로 검색할 문자열의 길이를 전달할 수 있다.

```javascript
const str = 'Hello World';

// 문자열 str의 처음부터 5자리까지('Hello')가 'lo'로 끝나는지 확인
str.endsWith('lo', 5);
```

### 32.2.6 String.prototype.charAt

인수로 전달받은 인덱스에 위치한 문자를 검색하여 반환한다.

```javascript
const str = 'Hello';

for(let i = 0; i < str.length; i++) {
    console.log(str.charAt(i)); // H e l l o
}
```

인덱스는 문자열의 범위, 즉 0 \~ (문자열 길이 - 1) 사이의 정수이어야 한다.

인덱스가 문자열의 범위를 벗어난 정수인 경우 빈 문자열을 반환.

```javascript
const str = 'Hello';

// 인덱스가 문자열의 범위(0 ~ str.length - 1)를 벗어난 경우 빈 문자열을 반환한다.
str.charAt(5); // ''
```
