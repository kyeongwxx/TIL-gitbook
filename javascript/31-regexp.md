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

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption><p>정규 표현식 리터럴</p></figcaption></figure>

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

정규 표현식의 패턴은 문자열의 일정한 규칙을 표현하기 위해 사용한다.

패턴은 /로 열고 닫으며 문자열의 따옴표는 생략한다. 따옴표를 포함하면 따옴표까지 패턴에 포함되어 검색된다. 또한 패턴은 특별한 의미를 가지는 메타문자(meta character) 또는 기호로 표현할 수 있다.

### 31.5.1 문자열 검색

패턴에 문자 또는 문자열을 지정하면 검색 대상 문자열에서 패턴으로 지정한 문자 또는 문자열을 검색한다.

```javascript
const target = 'Is this all there is?';

// 'is' 문자열과 매치하는 패턴. 플래그가 생략되었으므로 대소문자를 구별한다.
const regExp = /is/;

// target과 정규 표현식이 매치하는지 테스트한다.
regExp.test(target); // true

// target과 정규 표현식의 매칭 결과를 구한다.
target.match(regExp);
// [ 'is', index: 5, input: 'Is this all there is?', groups: undefined ]
```

대소문자를 구별하지 않고 검색하려면 플래그 i를 사용한다.

```javascript
const target = 'Is this all there is?';

// 'is' 문자열과 매치하는 패턴. 플래그가 i를 추가하면 대소문자를 구별하지 않는다.
const regExp = /is/i;

target.match(regExp);
// [ 'Is', index: 0, input: 'Is this all there is?', groups: undefined ]
```

검색 대상 문자열 내에서 패턴과 일치하는 모든 문자열을 전역 검색하려면 플래그 g를 사용한다.

```javascript
const target = 'Is this all there is?';

// 'is' 문자열과 매치하는 패턴.
// 플래그가 g를 추가하면 대상 문자열 내에서 패턴과 일치하는 모든 문자열을 전역 검색한다.
const regExp = /is/ig;

target.match(regExp); // [ "Is", "is", "is" ]
```

### 31.5.2 임의의 문자열 검색

.은 임의의 문자 한 개를 의미한다.

```javascript
const target = 'Is this all there is?';

// 임의의 3자리 문자열을 대소문자를 구별하여 전역 검색한다.
const regExp = /.../g;

target.match(regExp); // (7) ['Is ', 'thi', 's a', 'll ', 'the', 're ', 'is?']
```

### 31.5.3 반복 검색

{m,n}은 앞선 패턴이 최소 m번, 최대 n번 반복되는 문자열을 의미한다.

```javascript
const target = 'A AA B BB Aa Bb AAA';

// 'A'가 최소 1번, 최대 2번 반복되는 문자열을 전역 검색한다.
const regExp = /A{1,2}/g;

target.match(regExp); // ["A", "AA", "A", "AA", "A"]
```

{n}은 앞선 패턴이 n번 반복되는 문자열을 의미한다. 즉 {n}은 {n,n}과 같다.

```javascript
const target = 'A AA B BB Aa Bb AAA';

// 'A'가 2번 반복되는 문자열을 전역 검색한다.
const regExp = /A{2}/g;

target.match(regExp); // ["AA", "AA"]
```

{n,}은 앞선 패턴이 최소 n번 이상 반복되는 문자열을 의미한다.

```javascript
const target = 'A AA B BB Aa Bb AAA';

// 'A'가 최소 2번 반복되는 문자열을 전역 검색한다.
const regExp = /A{2,}/g;

target.match(regExp); // ["AA", "AAA"]
```

\+는 앞선 패턴이 최소 한번 이상 반복되는 문자열을 의미한다. 즉, +는 {1,}과 같다.

```javascript
const target = 'A AA B BB Aa Bb AAA';

// 'A'가 최소 1번 반복되는 문자열을 전역 검색한다.
const regExp = /A+/g;

target.match(regExp); // ["A", "AA", "A", "AAA"]
```

?는 앞선 패턴이 최대 한번 이상 반복되는 문자열을 의미한다. 즉, ?는 {0, 1}과 같다.

```javascript
const target = 'color colour';

// 'colo' 다음 'u'가 0이나 1번 반복
const regExp = /colou?r/g;

target.match(regExp); // ["color", "colour"]
```

### 31.5.4 OR 검색

|는 or의 의미를 갖는다.

```javascript
const target = 'A AA B BB Aa Bb';

// 'A' 또는 'B'를 전역 검색한다.
const regExp = /A|B/g;

target.match(regExp); // ["A", "A", "A", "B", "B", "B", "A", "B"]
```

분해되지 않은 단어 레벨로 검색하기 위해서는 +를 함께 사용한다.

```javascript
const target = 'A AA B BB Aa Bb';

// 'A' 또는 'B'가 한 번 이상 반복되는 문자열을 전역 검색한다.
// 'A', 'AA', 'AAA', ... 또는 'B', 'BB', 'BBB', ...
const regExp = /A+|B+/g;

target.match(regExp); // ["A", "AA", "B", "BB", "A", "B"]
```

\[] 내의 문자는 or로 동작한다. 그 뒤에 +를 사용하면 앞선 패턴을 한 번 이상 반복한다.

```javascript
const target = 'A AA B BB Aa Bb';

// 'A' 또는 'B'가 한 번 이상 반복되는 문자열을 전역 검색한다.
// 'A', 'AA', 'AAA', ... 또는 'B', 'BB', 'BBB', ...
const regExp = /[AB]+/g;

target.match(regExp); // ["A", "AA", "B", "BB", "A", "B"]
```

범위를 지정하려면 \[] 내에 -를 사용한다.

```javascript
const target = 'A AA BB ZZ Aa Bb';

// 'A' ~ 'Z'가 한 번 이상 반복되는 문자열을 전역 검색한다.
// 'A', 'AA', 'AAA', ... 또는 'B', 'BB', 'BBB', ... 또는 'Z', 'ZZ', 'ZZZ', ...
const regExp = /[A-Z]+/g;

target.match(regExp); // ["A", "AA", "BB", "ZZ", "A", "B"]
```

대소문자를 구별하지 않고 알파벳을 검색하는 방법은 다음과 같다.

```javascript
const target = 'AA BB Aa Bb 12';

// 'A' ~ 'Z' 또는 'a' ~ 'z'가 한 번 이상 반복되는 문자열을 전역 검색한다.
// 'A', 'AA', 'AAA', ... 또는 'B', 'BB', 'BBB', ... 또는 'Z', 'ZZ', 'ZZZ', ...
const regExp = /[A-Za-z]+/g;

target.match(regExp); // ["AA", "BB", "Aa", "Bb"]
```

숫자를 검색하는 방법은 다음과 같다.

```javascript
const target = 'AA BB 12,345';

// '0' ~ '9'가 한 번 이상 반복되는 문자열을 전역 검색한다.
const regExp = /[0-9]+/g;

target.match(regExp); // ["12", "345"]
```

쉼표를 패턴에 포함시킨다.

```javascript
const target = 'AA BB 12,345';

// '0' ~ '9' 또는 ','가 한 번 이상 반복되는 문자열을 전역 검색한다.
const regExp = /[0-9,]+/g;

target.match(regExp); // ["12,345"]
```

위 예제를 간단히 표현하면 다음과 같다. \d는 숫자를 의미한다. 즉 \d는 \[0-9]와 같다. \D는 숫자가 아닌 문자를 의미한다.

```javascript
const target = 'AA BB 12,345';

// '0' ~ '9' 또는 ','가 한 번 이상 반복되는 문자열을 전역 검색한다.
let regExp = /[\d,]+/g;

target.match(regExp); // ["12,345"]

// '0' ~ '9'가 아닌 문자 또는 ','가 한 번 이상 반복되는 문자열을 전역 검색한다.
regExp = /[\D,]+/g;

target.match(regExp); // ["AA BB ", ","]
```

\w는 알파벳, 숫자, 언더스코어를 의미한다. 즉, \w는 \[A-Za-z0-9\_]와 같다. \W는 알파벳, 숫자, 언더스코어가 아닌 문자를 의미한다.

```javascript
const target = 'Aa Bb 12,345 _$%&';

// 알파벳, 숫자, 언더스코어, ','가 한 번 이상 반복되는 문자열을 전역 검색한다.
let regExp = /[\w,]+/g;

target.match(regExp); // ["Aa", "Bb", "12,345", "_"]

// 알파벳, 숫자, 언더스코어가 아닌 문자 또는 ','가 한 번 이상 반복되는 문자열을 전역 검색한다.
regExp = /[\W,]+/g;

target.match(regExp); // [" ", " ", ",", " $%&"]
```

### 31.5.5 NOT 검색

\[...] 내의 ^은 not의 의미를 갖는다.

\[0-9]는 \d와 같고, \[^0-9]는 \D와 같다.

\[A-Za-z0-9\_]는 \w와 같고, \[^A-Za-z0-9\_]는 \W와 같다.

```javascript
const target = 'AA BB 12 Aa Bb';

// 숫자를 제외한 문자열을 전역 검색한다.
const regExp = /[^0-9]+/g;

target.match(regExp); // ["AA BB ", " Aa Bb"]
```

### 31.5.6 시작 위치로 검색

\[...] 밖의 ^은 문자열의 시작을 의미한다.

```javascript
const target = 'https://poiemaweb.com';

// 'https'로 시작하는지 검사한다.
const regExp = /^https/;

regExp.test(target); // true
```

### 31.5.7 마지막 위치로 검색

$는 문자열의 마지막을 의미한다.

```javascript
const target = 'https://poiemaweb.com';

// 'com'으로 끝나는지 검사한다.
const regExp = /com$/;

regExp.test(target); // true
```

## 31.6 자주 사용하는 정규 표현식

### 31.6.1 특정 단어로 시작하는지 검사

\[...] 밖의 ^은 문자열의 시작을 의미.

?는 앞선 패턴이 0번 또는 1번 반복되는지 의미.

```javascript
const url = 'https://example.com';

// 'http://' 또는 'https://'로 시작하는지 검사한다.
/^https?:\/\//.test(url); // true
```

다음 방법도 동일하게 동작한다.

```javascript
/^(http|https):\/\//.test(url);
```

### 31.6.2 특정 단어로 끝나는지 검사

$는 문자열의 마지막을 의미.

```javascript
const fileName = 'index.html';

// 'html'로 끝나는지 검사한다.
/html$/.test(fileName); // true
```

### 31.6.3 숫자로만 이루어진 문자열인지 검사

\[...] 밖의 ^은 문자열의 시작을 의미.

$는 문자열의 마지막을 의미.

\d는 숫자를 의미.

\+는 앞선 패턴이 최소 한 번 이상 반복되는 문자열을 의미.

즉, 처음과 끝이 숫자이고 최소 한 번 이상 반복되는 문자열과 매치.

```javascript
const target = '12345';

// 숫자로만 이루어진 문자열인지 검사한다.
/^\d+$/.test(target); // true
```

### 31.6.4 하나 이상의 공백으로 시작하는지 검사

\s는 여러 가지 공백 문자(스페이스, 탭 등)를 의미.

즉, \s는 \[\t\r\n\v\f]와 같은 의미.

```javascript
const target = ' Hi!';

// 하나 이상의 공백으로 시작하는지 검사한다.
/^[\s]+/.test(target); // true
```

### 31.6.5 아이디로 사용 가능한지 검사

{4,10}은 앞선 패턴이 최소 4번, 최대 10번 반복되는 문자열을 의미.

```javascript
const id = 'abc123';

// 알파벳 대소문자 또는 숫자로 시작하고 끝나며 4~10자리인지 검사한다.
/^[A-Za-z0-9]{4,10}$/.test(id); // true
```

### 31.6.6 메일 주소 형식에 맞는지 검사

```javascript
const email = 'abc123@gmail.com';

/^[0-9a-zA-Z]([-_\.]?[0-9a-zA-Z]*@[0-9a-zA-Z])([-_\.]?[0-9a-zA-Z])*\.[a-zA-Z]{2,3}$/.test(email); // true
```

### 31.6.7 핸드폰 번호 형식에 맞는지 검사

```javascript
const cellphone = '010-1234-5678';

/^\d{3}-\d{3,4}-\d{4}$/.test(cellphone); // true
```

### 31.6.8 특수 문자 포함 여부 검사

특수 문자는 A-Za-z0-9 이외의 문자다.

```javascript
const target = 'abc#123';

(/[^A-Za-z0-9]/gi).test(target); // true
```

특수 문자를 제거할 때는 replace 메서드를 사용한다.

```javascript
const target = 'abc#123';

// 특수 문자를 제거한다.
target.replace(/[^A-Za-z0-9]/gi, ''); // "abc123"
```

> Reference
>
> 모던 자바스크립트 DEEP DIVE
>
> **2023.01.27**
