# 31-RegExp

## 31.1 정규 표현식이란?

정규 표현식(regular expression)은 일정한 패턴을 가진 문자열의 집합을 표현하기 위해 사용하는 형식 언어(formal language)다.

정규 표현식은 문자열을 대상으로 <mark style="color:purple;">**패턴 매칭 기능**</mark>을 제공한다. 패턴 매칭 기능이란 특정 패턴과 일치하는 문자열을 검색하거나 추출 또는 치환할 수 있는 기능을 말한다.

```javascript
// 사용자로부터 입력받은 휴대폰 전화번호
const tel = '010-1234-567팔';

// 정규 표현식 리터럴로 휴대폰 전화번호 패턴을 정의한다.
const regExp = /^d{3}-\d{4}-\d{4}$/;

// tel이 휴대폰 전화번호 패턴에 매칭하는지 테스트(확인)한다.
regExp.test(tel); // false
```

## 31.2 정규 표현식의 생성

정규 표현식 객체(RegExp 객체)를 생성하기 위해서는 정규 표현식 리터럴과 RegExp 생성자 함수를 사용할 수 있다.

정규 표현식 리터럴은 다음과 같이 표현한다.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption><p>정규 표현식 리터럴</p></figcaption></figure>

```javascript
const target = 'Is this all there is?';

// 패턴: is
// 플래그: i => 대소문자를 구별하지 않고 검색한다.
const regexp = /is/i;

// test 메소드는 target 문자열에 대해 정규 표현식 regexp의 패턴을 검색하여 매칭 결과를 불리언 값으로 반환한다.
regexp.test(target); // true
```

RegExp 생성자 함수를 사용하여 RegExp 객체를 생성할 수도 있다.

```javascript
const target = 'Is this all there is?';

const regexp = new RegExp(/is/i); // ES6
// const regexp = new RegExp(/is/, 'i');
// const regexp = new RegExp('is', 'i');

regexp.test(target); // true
```

RegExp 생성자 함수를 사용하면 변수를 사용해 동적으로 RegExp 객체를 생성할 수 있다.

```javascript
const count = (str, char) => (str.match(new RegExp(char, 'gi')) ?? []).length;

count('Is this all there is?', 'is'); // 3
count('Is this all there is?', 'xx'); // 0
```

## 31.3 RegExp 메서드

### 31.3.1 RegExp.prototype.exec

인수로 전달받은 문자열에 대해 정규 표현식의 패턴을 검색하여 매칭 결과를 배열로 반환한다. 매칭 결과가 없는 경우 null을 반환한다.

문자열 내의 모든 패턴을 검색하는 g 플래그를 지정해도 첫 번째 매칭 겨로가만 반환한다.

```javascript
const target = 'Is this all there is?';
const regExp = /is/;

regExp.exec(target);
// ['is', index: 5, input: 'Is this all there is?', groups: undefined]
```

### 31.3.2 RegExp.prototype.test

인수로 전달받은 문자열에 대해 정규 표현식의 패턴을 검색하여 매칭 결과를 불리언 값으로 반환한다.

```javascript
const target = 'Is this all there is?';
const regExp = /is/;

regExp.test(target); // true
```

### 31.3.3 String.prototype.match

대상 문자열과 인수로 전달받은 정규 표현식과의 매칭 결과를 배열로 반환한다.

```javascript
const target = 'Is this all there is?';
const regExp = /is/;

target.match(regExp);
// ['is', index: 5, input: 'Is this all there is?', groups: undefined]
```

g 플래그가 지정되면 모든 매칭 결과를 배열로 반환한다.

```javascript
const target = 'Is this all there is?';
const regExp = /is/g;

target.match(regExp); // (2) ['is', 'is']
```

## 31.4 플래그

패턴과 함께 정규 표현식을 구성하는 플래그는 정규 표현식의 검색 방식을 설정하기 위해 사용한다.

| 플래그 |      의미     |                  설명                  |
| :-: | :---------: | :----------------------------------: |
|  i  | Ignore case |        대소문자를 구별하지 않고 패턴을 검색한다.       |
|  g  |    Global   | 대상 문자열 내에서 패턴과 일치하는 모든 문자열을 전역 검색한다. |
|  m  |  Multi line |      문자열의 행이 바뀌더라도 패턴 검색을 계속한다.      |

플래그는 옵션이며 동시에 설정할 수도 있다. 생략 시 대소문자를 구별해서 한 번만 검색한다.

```javascript
const target = 'Is this all there is?';

// target 문자열에서 is 문자열을 대소문자를 구별하여 한 번만 검색한다.
target.match(/is/);
// [ 'is', index: 5, input: 'Is this all there is?', groups: undefined ]

// target 문자열에서 is 문자열을 대소문자를 구별하지 않고 한 번만 검색한다.
target.match(/is/i);
// [ 'Is', index: 0, input: 'Is this all there is?', groups: undefined ]

// target 문자열에서 is 문자열을 대소문자를 구별하여 전역 검색한다.
target.match(/is/g);
// [ 'is', 'is' ]

// target 문자열에서 is 문자열을 대소문자를 구별하지 않고 전역 검색한다.
target.match(/is/ig);
// [ 'Is', 'is', 'is' ]
```

## 31.5 패턴
