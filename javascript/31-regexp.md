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
