# 39-DOM

<mark style="color:purple;">**DOM(Document Object Model)은 HTML 문서의 계층적 구조와 정보를 표현하며 이를 제어할 수 있는 API, 즉 프로퍼티와 메서드를 제공하는 트리 자료구조**</mark>다.

## 39.1 노드

### 39.1.1 HTML 요소와 노드 객체

HTML 요소는 HTML 문서를 구성하는 개별적인 요소를 의미한다.

<figure><img src="../.gitbook/assets/image (2) (4).png" alt=""><figcaption></figcaption></figure>

HTML 요소는 렌더링 엔진에 의해 파싱되어 DOM을 구성하는 요소 노드 객체로 변환된다. 이때 HTML 요소의 어트리뷰트는 어트리뷰트 노드로, 텍스트 콘텐츠는 텍스트 노드로 변환된다.

<figure><img src="../.gitbook/assets/image (4) (5).png" alt=""><figcaption></figcaption></figure>

HTML 문서는 HTML 요소들의 집합으로 이뤄지며, HTML 요소는 중첩 관계를 갖는다. 즉, HTML 요소의 콘텐츠 영역에는 텍스트뿐만 아니라 다른 HTML 요소도 포함할 수 있다.

이때 HTML 요소 간에는 중첩 관계에 의해 계층적인 부자 관계가 형성된다. 이러한 HTML 요소 간의 부자 관계를 반영하여 HTML 문서의 구성 요소인 HTML 요소를 객체화한 모든 노드 객체들을 트리 자료구조로 구성한다.

트리 자료구조

트리 자료구조(tree data structure)는 노드들의 계층 구조로 이뤄진다. 즉, 트리 자료구조는 부모 노드와 자식 노드로 구성되어 노드 간의 계층적 구조(부자, 형제 관계)를 표현하는 비선형 자료구조를 말한다. 트리 자료구조는 하나의 최상위 노드에서 시작한다. 최상위 노드는 부모 노드가 없으며, 루트 노드라 칭한다. 루트 노드는 0개 이상의 자식 노드를 갖는다. 자식 노드가 없는 노드를 리프 노드라 한다.

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

<mark style="color:purple;">**노드 객체들로 구성된 트리 자료구조를 DOM**</mark>이라 한다. 노드 객체의 트리로 구조화되어 있기 때문에 <mark style="color:purple;">**DOM 트리**</mark>라고 부르기도 한다.

### 39.1.2 노드 객체의 타입

예를 들어, 다음 HTML 문서를 렌더링 엔진이 파싱한다고 생각해보자.

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <link rel="stylesheet" href="style.css">
    </head>
    <body>
        <ul>
            <li id="apple">Apple</li>
            <li id="banana">Banana</li>
            <li id="orange">Orange</li>
        </ul>
        <script src="app.js"></script>
    </body>
</html>
```

렌더링 엔진은 위 HTML 문서를 파싱하여 다음과 같이 DOM을 생성한다.

<figure><img src="../.gitbook/assets/image (1) (1) (3).png" alt=""><figcaption></figcaption></figure>

이처럼 DOM은 노드 객체의 계층적인 구조로 구성된다. 노드 객체는 종류가 있고 상속 구조를 갖는다.

<mark style="color:green;">**문서 노드(document node)**</mark>

문서 노드는 DOM 트리의 최상위에 존재하는 루트 노드로서 document 객체를 가리킨다. document 객체는 브라우저가 렌더링한 HTML 문서 전체를 가리키는 객체로서 전역 객체 window의 document 프로퍼티에 바인딩되어 있다. 따라서 문서 노드는 window.document 또는 document로 참조할 수 있다.

브라우저 환경의 모든 자바스크립트 코드는 script 태그에 의해 분리되어 있어도 하나의 전역 객체 window를 공유한다. 따라서 모든 자바스크립트 코드는 전역 객체 window의 document 프로퍼티에 바인딩되어 있는 하나의 document 객체를 바라본다. 즉, HTML 문서당 document 객체는 유일하다.

문서 노드, 즉 document 객체는 DOM 트리의 루트 노드이므로 DOM 트리의 노드들에 접근하기 위한 진입점(entry point) 역할을 담당한다. 즉, 요소, 어트리뷰트, 텍스트 노드에 접근하려면 문서 노드를 통해야 한다.

<mark style="color:green;">**요소 노드(element node)**</mark>

요소 노드는 HTML 요소를 가리키는 객체다. 요소 노드는 HTML 요소 간의 중첩에 의해 부자 관계를 가지며, 이 부자 관계를 통해 정보를 구조화한다. 따라서 요소 노드는 문서의 구조를 표현한다고 할 수 있다.

<mark style="color:green;">**어트리뷰트 노드(attribute node)**</mark>

어트리뷰트 노드는 HTML 요소의 어트리뷰트를 가리키는 객체다. 어트리뷰트 노드는 어트리뷰트가 지정된 HTML 요소의 요소 노드와 연결되어 있다. 단, 요소 노드는 부모 노드와 연결되어 있지만 어트리뷰트 노드는 부모 노드와 연결되어 있지 않고 요소 노드에만 연결되어 있다. 즉, 어트리뷰트 노드는 부모 노드가 없으므로 요소 노드의 형제(sibling) 노드는 아니다. 따라서 어트리뷰트 노드에 접근하여 어트리뷰트를 참조하거나 변경하려면 먼저 요소 노드에 접근해야 한다.

텍스트 노드(text node)

텍스트 노드는 HTML 요소의 텍스트를 가리키는 객체다. 요소 노드가 문서의 구조를 표현한다면 텍스트 노드는 문서의 정보를 표현한다고 할 수 있다. 텍스트 노드는 요소 노드의 자식 노드이며, 자식 노드를 가질 수 없는 리프 노드(leaf node)이다. 즉, 텍스트 노드는 DOM 트리의 최종단이다. 따라서 텍스트 노드에 접근하려면 먼저 부모 노드인 요소 노드에 접근해야 한다.

### 39.1.3 노드 객체의 상속 구조

DOM은 HTML 문서의 계층적 구조와 정보를 표현하며, 이를 제어할 수 있는 API, 즉 프로퍼티와 메서드를 제공하는 트리 자료구조라고 했다. 즉, DOM을 구성하는 노드 객체는 자신의 구조와 정보를 제어할 수 있는 DOM API를 사용할 수 있다. 이를 통해 노드 객체는 자신의 부모, 형제, 자식을 탐색할 수 있으며, 자신의 어트리뷰트와 텍스트를 조작할 수도 있다.

DOM을 구성하는 노드 객체는 ECMAScript 사양에 정의된 표준 빌트인 객체가 아니라 브라우저 환경에서 추가적으로 제공하는 호스트 객체다. 하지만 노드 객체도 자바스크립트 객체이므로 프로토타입에 의한 상속 구조를 갖는다. 노드 객체의 상속 구조는 다음과 같다.

<figure><img src="../.gitbook/assets/image (4) (4).png" alt=""><figcaption></figcaption></figure>

위 그림과 같이 모든 노드 객체는 Object, EventTarget, Node 인터페이스를 상속받는다. 추가적으로 문서 노드는 Document, HTMLDocument 인터페이스를 상속받고 어트리뷰트 노드는 Attr, 텍스트 노드는 CharacterData 인터페이스를 각각 상속받는다.

요소 노드는 Element 인터페이스를 상속받는다. 또한 요소 노드는 추가적으로 HTMLElement와 태그의 종류별로 세분화된 HTMLHtmlElement, HTMLHeadElement 등의 인터페이스를 상속받는다.

이를 프로토타입 체인 관점에서 살펴보자. 예를 들어, input 요소를 파싱하여 객체화한 input 요소 노드 객체는 HTMLInputElement, HTMLElement, Element, Node, EventTarget, Object의 prototype에 바인딩되어 있는 프로토타입 객체를 상속받는다. 즉, input 요소 노드 객체는 프로토타입 체인에 있는 모든 프로토타입의 프로퍼티나 메서드를 상속받아 사용할 수 있다.

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

```html
<!DOCTYPE html>
<html>
    <body>
        <input type="text">
        <script>
            // input 요소 노드 객체를 선택
            const $input = document.querySelector('input');

            // input 요소 노드 객체의 프로토타입 체인
            console.log(
                Object.getPrototypeOf($input) === HTMLInputElement.prototype,
                Object.getPrototypeOf(HTMLInputElement.prototype) === HTMLElement.prototype,
                Object.getPrototypeOf(HTMLElement.prototype) === Element.prototype,
                Object.getPrototypeOf(Element.prototype) === Node.prototype,
                Object.getPrototypeOf(Node.prototype) === EventTarget.prototype,
                Object.getPrototypeOf(EventTarget.prototype) === Object.prototype
            ); // 모두 true
        </script>
    </body>
</html>
```

배열이 객체인 동시에 배열인 것처럼 input 요소 노드 객체도 다음과 같이 다양한 특성을 갖는 객체이며, 이러한 특성을 나타내는 기능들을 상속을 통해 제공받는다.

|                 input 요소 노드 객체의 특성                |  프로토타입을 제공하는 객체  |
| :-----------------------------------------------: | :--------------: |
|                         객체                        |      Object      |
|                   이벤트를 발생시키는 객체                   |    EventTarget   |
|                   트리 자료구조의 노드 객체                  |       Node       |
| 브라우저가 렌더링할 수 있는 웹 문서의 요소(HTML, XML, SVG)를 표현하는 객체 |      Element     |
|           웹 문서의 요소 중에서 HTML 요소를 표현하는 객체           |    HTMLElement   |
|           HTML 요소 중에서 input 요소를 표현하는 객체           | HTMLInputElement |

노드 객체에는 노드 객체의 종류, 즉 노드 타입에 상관없이 모든 노드 객체가 공통으로 갖는 기능도 있고, 노드 타입에 따라 고유한 기능도 있다. 예를 들어, 모든 노드 객체는 공통적으로 이벤트를 발생시킬 수 있다.

이벤트에 관련된 기능(EventTarget.addEventListener 등)은 EventTarget 인터페이스가 제공한다. 또한 모든 노드 객체는 트리 자료구조의 노드로서 공통적으로 트리 탐색 기능(Node.parentNode 등)이나 노드 정보 제공 기능(Node.nodeType 등)이 필요하다. 이 같은 노드 관련 기능은 Node 인터페이스가 제공한다.

HTML 요소가 객체화된 요소 노드 객체는 HTML 요소가 갖는 공통적인 기능이 있다. 예를 들어, input 요소 객체와 div 요소 객체는 모두 HTML 요소의 스타일을 나타내는 style 프로퍼티가 있다. 이처럼 HTML 요소가 갖는 공통적인 기능은 HTMLElement 인터페이스가 제공한다.

하지만 요소 노드 객체는 HTML 요소의 종류에 따라 고유한 기능도 있다. 예를 들어, input 요소 노드 객체는 value 프로퍼티가 필요하지만 div 요소 노드 객체는 value 프로퍼티가 필요하지 않다. 따라서 필요한 기능을 제공하는 인터페이스(HTMLInputElement, HTMLDivElement)가 HTML 요소의 종류에 따라 각각 다르다.

이처럼 노드 객체는 공통된 기능일수록 프로토타입 체인의 상위에, 개별적인 고유 기능일수록 프로토타입 체인의 하위에 프로토타입 체인을 구축하여 노드 객체에 필요한 기능, 즉 프로퍼티와 메서드를 제공하는 상속 구조를 갖는다.

지금까지 살펴본 바와 같이 <mark style="color:purple;">**DOM은 HTML 문서의 계층적 구조와 정보를 표현하는 것은 물론 노드 객체의 종류, 즉 노드 타입에 따라 필요한 기능을 프로퍼티와 메서드의 집합인 DOM API로 제공한다. 이 DOM API를 통해 HTML의 구조나 내용 또는 스타일 등을 동적으로 조작할 수 있다.**</mark>

## 39.2 요소 노드 취득

HTML의 구조나 내용 또는 스타일 등을 동적으로 조작하려면 먼저 요소 노드를 취득해야 한다. 텍스트 노드는 요소 노드의 자식 노드이고, 어트리뷰트 노드는 요소 노드와 연결되어 있기 때문에 텍스트 노드나 어트리뷰트 노드를 조작하고자 할 때도 마찬가지다.

### 39.2.1 id를 이용한 요소 노드 취득

Document.prototype.getElementById 메서드는 인수로 전달한 id 어트리뷰트 값을 갖는 하나의 요소 노드를 탐색하여 반환한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul>
            <li id="apple">Apple</li>
            <li id="banana">Banana</li>
            <li id="orange">Orange</li>
        </ul>
        <script>
            // id 값이 'banana'인 요소 노드를 탐색하여 반환한다.
            // 두 번째 li 요소가 파싱되어 생성된 요소 노드가 반환된다.
            const $elem = document.getElementById('banana');

            // 취득한 요소 노드의 style.color 프로퍼티 값을 변경한다.
            $elem.style.color = 'red';
        </script>
    </body>
</html>
```

id 값은 HTML 문서 내에서 유일한 값이어야 하며, class 어트리뷰트와는 달리 공백 문자로 구분하여 여러 개의 값을 가질 수 없다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul>
            <li id="banana">Apple</li>
            <li id="banana">Banana</li>
            <li id="banana">Orange</li>
        </ul>
        <script>
            // getElementById 메소드는 언제나 단 하나의 요소 노드를 반환한다.
            // 첫 번째 li 요소가 파싱되어 생성된 요소 노드가 반환된다.
            const $elem = document.getElementById('banana');

            // 취득한 요소 노드의 style.color 프로퍼티 값을 변경한다.
            $elem.style.color = 'red';
        </script>
    </body>
</html>
```

만약 인수로 전달된 id 값을 갖는 HTML 요소가 존재하지 않는 경우 getElementById 메서드는 null을 반환한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul>
            <li id="apple">Apple</li>
            <li id="banana">Banana</li>
            <li id="orange">Orange</li>
        </ul>
        <script>
            // id 값이 'grape'인 요소 노드를 탐색하여 반환한다. null이 반환된다.
            const $elem = document.getElementById('grape');

            // 취득한 요소 노드의 style.color 프로퍼티 값을 변경한다.
            $elem.style.color = 'red'; // TypeError: Cannot read properties of null (reading 'style')
        </script>
    </body>
</html>
```

HTML 요소에 id 어트리뷰트를 부여하면 id 값과 동일한 이름의 전역 변수가 암묵적으로 선언되고 해당 노드 객체가 할당되는 부수 효과가 있다.

```html
<!DOCTYPE html>
<html>
    <body>
        <div id="foo"></div>
        <script>
            // id 값과 동일한 이름의 전역 변수가 암묵적으로 선언되고 해당 노드 객체가 할당된다.
            console.log(foo === document.getElementById('foo')); // true

            // 암묵적 전역으로 생성된 전역 프로퍼티는 삭제되지만 전역 변수는 삭제되지 않는다.
            delete foo;
            console.log(foo); // <div id="foo"></div>
        </script>
    </body>
</html>
```

단, id 값과 동일한 이름의 전역 변수가 이미 선언되어 있으면 이 전역 변수에 노드 객체가 재할당되지 않는다.

```html
<!DOCTYPE html>
<html>
    <body>
        <div id="foo"></div>
        <script>
            let foo = 1;

            // id 값과 동일한 이름의 전역 변수가 이미 선언되어 있으면 노드 객체가 재할당되지 않는다.
            delete foo;
            console.log(foo); // 1
        </script>
    </body>
</html>
```

### 39.2.2 태그 이름을 이용한 요소 노드 취득

Document.prototype.getElementsByTagName 메서드는 인수로 전달한 태그 이름을 갖는 모든 요소 노드들을 탐색하여 반환한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul>
            <li id="apple">Apple</li>
            <li id="banana">Banana</li>
            <li id="orange">Orange</li>
        </ul>
        <script>
            // 태그 이름이 li인 요소 노드를 모두 탐색하여 반환한다.
            // 탐색된 요소 노드들은 HTMLCollection 객체에 담겨 반환된다.
            // HTMLCollection 객체는 유사 배열 객체이면서 이터러블이다.
            const $elems = document.getElementsByTagName('li');

            // 취득한 요소 노드의 style.color 프로퍼티 값을 변경한다.
            // HTMLCollection 객체를 배열로 변환하여 순회하며 color 프로퍼티 값을 변경한다.
            [...$elems].forEach(elem => { elem.style.color = 'red'; });
        </script>
    </body>
</html>
```

함수는 하나의 값만 반환할 수 있으므로 여러 개의 값을 반환하려면 배열이나 객체와 같은 자료구조에 담아 반환해야 한다. getElementsByTagName 메서드가 반환하는 DOM 컬렉션 객체인 HTMLCollection 객체는 유사 배열 객체이면서 이터러블이다.

HTML 문서의 모든 요소 노드를 취득하려면 getElementsByTagName 메서드의 인수로 '\*'를 전달한다.

```javascript
// 모든 요소 노드를 탐색하여 반환한다.
const $all = document.getElementsByTagName('*');
// -> HTMLCollection(8) [html, head, body, ul, li#apple, li#banana, li#orange,
// script, apple:li#apple, banana:li#banana, orange:li#orange]
```

getElementsByTagName 메서드는 Document.prototype에 정의된 메서드와 Element.prototype에 정의된 메서드가 있다.

* Document: DOM의 루트 노드인 문서 노드, 즉 document를 통해 호출하여 DOM 전체에서 요소 노드를 탐색하여 반환.
* Element: 특정 요소 노드를 통해 호출하며, 특정 요소 노드의 자손 노드 중에서 요소 노드를 탐색하여 반환.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul id="fruits">
            <li id="apple">Apple</li>
            <li id="banana">Banana</li>
            <li id="orange">Orange</li>
        </ul>
        <ul>
            <li>HTML</li>
        </ul>
        <script>
            // DOM 전체에서 태그 이름이 li인 요소 노드를 모두 탐색하여 반환한다.
            const $lisFromDocument = document.getElementsByTagName('li');
            console.log($lisFromDocument) // HTMLCollection(4) [li, li, li, li]

            // ul#fruits 요소의 자손 노드 중에서 태그 이름이 li인 요소 노드를 모두 탐색하여 반환한다.
            const $fruits = document.getElementById('fruits');           
            const $lisFromFruits = $fruits.getElementsByTagName('li');
            console.log($lisFromFruits); // HTMLCollection(3) [li, li, li]
        </script>
    </body>
</html>
```

만약 인수로 전달된 태그 이름을 갖는 요소가 존재하지 않는 경우 빈 HTMLCollection 객체를 반환한다.

### 39.2.3 class를 이용한 요소 노드 취득

Document.prototype.getElementsByClassName 메서드는 인수로 전달한 class 어트리뷰트 값을 갖는 모든 요소들을 탐색하여 반환한다. 인수로 전달할 class 값은 공백으로 구분하여 여러 개의 class를 지정할 수 있다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul>
            <li class="fruit apple">Apple</li>
            <li class="fruit banana">Banana</li>
            <li class="fruit orange">Orange</li>
        </ul>
        <script>
            // class 값이 'fruit'인 요소 노드를 모두 탐색하여 HTMLCollection 객체에 담아 반환한다.
            const $elems = document.getElementsByClassName('fruit');

            // 취득한 모든 요소의 CSS color 프로퍼티 값을 변경한다.
            [...$elems].forEach(elem => { elem.style.color = 'red' });
            
            // class 값이 'fruit apple'인 요소 노드를 모두 탐색하여 HTMLCollection 객체에 담아 반환한다.
            const $apples = document.getElementsByClassName('fruit apple');
            
            // 취득한 모든 요소의 style.color 프로퍼티 값을 변경한다.
            [...$apples].forEach(elem => { elem.style.color = 'blue' });
        </script>
    </body>
</html>
```

getElementsByClassName 메서드는 Document.prototype에 정의된 메서드와 Element.prototype에 정의된 메서드가 있다.

* Document: DOM의 루트 노드인 문서 노드, 즉 document를 통해 호출하여 DOM 전체에서 요소 노드를 탐색하여 반환.
* Element: 특정 요소 노드를 통해 호출하며, 특정 요소 노드의 자손 노드 중에서 요소 노드를 탐색하여 반환.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul id="fruits">
            <li class="apple">Apple</li>
            <li class="banana">Banana</li>
            <li class="orange">Orange</li>
        </ul>
        <div class="banana">Banana</div>
        <script>
            // DOM 전체에서 class 값이 'banana'인 요소 노드를 모두 탐색하여 반환한다.
            const $bananasFromDocument = document.getElementsByClassName('banana');
            console.log($bananasFromDocument)
            // HTMLCollection(2) [li.banana, div.banana]

            // #fruits 요소의 자손 노드 중에서 class 값이 'banana'인 요소 노드를 모두 탐색하여 반환한다.
            const $fruits = document.getElementById('fruits');
            const $bananasFromFruits = $fruits.getElementsByClassName('banana');
            console.log($bananasFromFruits);
            // HTMLCollection [li.banana]
        </script>
    </body>
</html>
```

만약 인수로 전달된 class 값을 갖는 요소가 존재하지 않는 경우 빈 HTMLCollection 객체를 반환한다.

### 39.2.4 CSS 선택자를 이용한 요소 노드 취득

CSS 선택자는 스타일을 적용하고자 하는 HTML 요소를 특정할 때 사용하는 문법이다.

```css
/* 전체 선택자: 모든 요소를 선택 */
* { ... }
/* 태그 선택자: 모든 p 태그 요소를 모두 선택 */
p { ... }
/* id 선택자: id 값이 'foo'인 요소를 모두 선택 */
#foo { ... }
/* class 선택자: class 값이 'foo'인 요소를 모두 선택 */
.foo { ... }
/* 어트리뷰트 선택자: input 요소 중에 type 어트리뷰트 값이 'text'인 요소를 모두 선택 */
input[type=text] { ... }
/* 후손 선택자: div 요소의 후손 요소 중 p 요소를 모두 선택 */
div p { ... }
/* 자식 선택자: div 요소의 자식 요소 중 p 요소를 모두 선택 */
div > p { ... }
/* 인접 형제 선택자: p 요소의 형제 요소 중에 p 요소 바로 뒤에 위치하는 ul 요소를 선택*/
p + ul { ... }
/* 일반 형제 선택자: p 요소의 형제 요소 중에 p 요소 뒤에 위치하는 ul 요소를 모두 선택*/
p ~ ul { ... }
/* 가상 클래스 선택자: hover 상태인 a 요소를 모두 선택 */
a:hover { ... }
/* 가상 요소 선택자: p 요소의 콘텐츠의 앞에 위치하는 공간을 선택 */
/* 일반적으로 content 프로퍼티와 함께 사용된다. */
p::before { ... }
```

Document.prototype.querySelector 메서드는 인수로 전달한 CSS 선택자를 만족시키는 하나의 요소 노드를 탐색하여 반환한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul>
            <li class="apple">Apple</li>
            <li class="banana">Banana</li>
            <li class="orange">Orange</li>
        </ul>
        <script>
            // class 어트리뷰트 값이 'banana'인 첫 번째 요소 노드를 탐색하여 반환한다.
            const $elem = document.querySelector('.banana');

            // 취득한 요소 노드의 style.color 프로퍼티 값을 변경한다.
            $elem.style.color = 'red';
        </script>
    </body>
</html>
```

Document.prototype.querySelectorAll 메서드는 인수로 전달한 CSS 선택자를 만족시키는 모든 요소 노드를 탐색하여 반환한다.

querySelectorAll 메서드는 여러 개의 요소 노드 객체를 갖는 DOM 컬렉션 객체인 NodeList 객체를 반환한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul>
            <li class="apple">Apple</li>
            <li class="banana">Banana</li>
            <li class="orange">Orange</li>
        </ul>
        <script>
            // ul 요소의 자식 요소인 li 요소를 모두 탐색하여 반환한다.
            const $elems = document.querySelectorAll('ul > li');
            // 취득한 요소 노드들은 Nodelist 객체에 담겨 반환된다.
            console.log($elems); // NodeList(3) [li.apple, li.banana, li.orange]

            // 취득한 모든 요소 노드의 style.color 프로퍼티 값을 변경한다.
            // NodeList는 forEach 메소드를 제공한다.
            $elems.forEach(elem => { elem.style.color = 'red'; });
        </script>
    </body>
</html>
```

HTML 문서의 모든 요소 노드를 취득하려면 querySelectorAll 메서드의 인수로 전체 선택자 '\*'를 전달한다.

```javascript
// 모든 요소 노드를 탐색하여 반환한다.
const $all = document.querySelectorAll('*');
// -> NodeList(8) [html, head, body, ul, li#apple, li#banana, li#orange, script]
```

querySelector, querySelectorAll 메서드는 Document.prototype에 정의된 메서드와 Element.prototype에 정의된 메서드가 있다.

* Document: DOM의 루트 노드인 문서 노드, 즉 document를 통해 호출하여 DOM 전체에서 요소 노드를 탐색하여 반환.
* Element: 특정 요소 노드를 통해 호출하며, 특정 요소 노드의 자손 노드 중에서 요소 노드를 탐색하여 반환.

### 39.2.5 특정 요소 노드를 취득할 수 있는지 확인

Element.prototype.matches 메서드는 인수로 전달한 CSS 선택자를 통해 특정 요소 노드를 취득할 수 있는지 확인한다. 이벤트 위임을 사용할 때 유용하다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul id="fruits">
            <li class="apple">Apple</li>
            <li class="banana">Banana</li>
            <li class="orange">Orange</li>
        </ul>
    </body>
    <script>
        const $apple = document.querySelector('.apple');

        // $apple 노드는 '#fruits > li.apple'로 취득할 수 있다.
        console.log($apple.matches('#fruits > li.apple')); // true

        // $apple 노드는 '#fruits > li.banana'로 취득할 수 없다.
        console.log($apple.matches('#fruits > li.banana')); // false
    </script>
</html>
```

### 39.2.6 HTMLCollection과 NodeList

DOM 컬렉션 객체인 HTMLCollection과 NodeList는 DOM API가 여러 개의 결과값을 반환하기 위한 DOM 컬렉션 객체다. HTMLCollection과 NodeList는 모두 유사 배열 객체이면서 이터러블이다. 따라서 for...if 문으로 순회할 수 있으며 스프레드 문법을 사용하여 간단히 배열로 변환할 수 있다.

HTMLCollection과 NodeList의 중요한 특징은 노드 객체의 상태 변화를 실시간으로 반영하는 <mark style="color:purple;">**살아 있는 객체**</mark>라는 것이다. HTMLCollection은 언제나 live 객체로 동작한다. 단, NodeList는 대부분의 경우 노드 객체의 상태 변화를 실시간으로 반영하지 않고 과거의 정적 상태를 유지하는 non-live 객체로 동작하지만 경우에 따라 live 객체로 동작할 때가 있다.

<mark style="color:green;">**HTMLCollection**</mark>

getElementsByTagName, getElementsByClassName 메서드가 반환하는 HTMLCollection 객체는 노드 객체의 상태 변화를 실시간으로 반영하는 살아 있는 DOM 컬렉션 객체다. 따라서 이를 살아 있는 객체라고 부르기도 한다.

<mark style="color:green;">**NodeList**</mark>

HTMLCollection 객체의 부작용을 해결하기 위해 querySelectorAll 메서드를 사용하는 방법도 있다. 이 메서드가 반환하는 NodeList 객체는 실시간으로 노드 객체의 상태 변경을 반영하지 않는다.

<mark style="color:purple;">**노드 객체의 상태 변경과 상관없이 안전하게 DOM 컬렉션을 사용하려면 HTMLCollection이나 NodeList 객체를 배열로 변환하여 사용하는 것을 권장**</mark>한다.

## 39.3 노드 탐색

요소 노드를 취득한 다음, 취득한 요소를 기점으로 DOM 트리의 노드를 옮겨 다니며 부모, 형제, 자식 노드 등을 탐색해야 할 때가 있다.

```html
<ul id="fruits">
    <li class="apple">Apple</li>
    <li class="banana">Banana</li>
    <li class="orange">Orange</li>
</ul>
```

ul#fruits 요소는 3개의 자식 요소를 갖는다. 이때 먼저 ul#fruits 요소 노드를 취득한 다음, 자식 노드를 모두 탐색하거나 하나만 탐색할 수 있다. li.banana 요소는 2개의 형제 요소와 부모 요소를 갖는다. 이때 먼저 li.banana 요소 노드를 취득한 다음, 형제 노드를 탐색하거나 부모 노드를 탐색할 수 있다.

이처럼 DOM 트리 상의 노드를 탐색할 수 있도록 Node, Element 인터페이스는 트리 탐색 프로퍼티를 제공한다.

* Node.prototype: parentNode, previousSibling, nextSibling, firstChild, lastChild, childNodes 프로퍼티 제공
* Element.prototype: previousElementSibling, nextElementSibling, firstElementChild, lastElementChild, children 프로퍼티 제공

<figure><img src="../.gitbook/assets/image (2) (3) (2).png" alt=""><figcaption></figcaption></figure>

### 39.3.1 공백 텍스트 노드

HTML 요소 사이의 스페이스, 탭, 개행 등의 공백(white space) 문자는 텍스트 노드를 생성한다. 이를 공백 텍스트 노드라 한다.

```html
<!DOCTYPE html>
<html>

<body>
    <ul id="fruits">
        <li class="apple">Apple</li>
        <li class="banana">Banana</li>
        <li class="orange">Orange</li>
    </ul>
</body>

</html>
```

텍스트 에디터에서 HTML 문서에 스페이스 키, 탭 키, 엔터 키 등을 입력하면 공백 문자가 추가된다. 위 HTML 문서에도 공백 문자가 포함되어 있다. 위 HTML 문서는 파싱되어 다음과 같은 DOM을 생성한다.

<figure><img src="../.gitbook/assets/image (1) (3).png" alt=""><figcaption></figcaption></figure>

### 39.9.2 자식 노드 탐색

자식 노드를 탐색하기 위해서는 다음과 같은 노드 탐색 프로퍼티를 사용한다.

|                                     |                                                                                                                   |
| :---------------------------------: | :---------------------------------------------------------------------------------------------------------------: |
|      Node.prototype.childNodes      |          자식 노드를 모두 탐색하여 DOM 컬렉션 객체인 NodeList에 담아 반환한다. <mark style="color:purple;">**(텍스트 노드 포함)**</mark>         |
|      Element.prototype.children     | 자식 노드 중에서 요소 노드만 모두 탐색하여 DOM 컬렉션 객체인 HTMLCollection에 담아 반환한다. <mark style="color:purple;">**(텍스트 노드 미포함)**</mark> |
|      Node.prototype.firstChild      |                                           첫 번째 자식 노드 반환(텍스트 노드 or 요소 노드)                                          |
|       Node.prototype.lastChild      |                                           마지막 자식 노드 반환(텍스트 노드 or 요소 노드)                                           |
| Element.prototype.firstElementChild |                                                첫 번째 자식 노드 반환(요소 노드)                                               |
|  Element.prototype.lastElementChild |                                                마지막 자식 노드 반환(요소 노드)                                                |

```html
<!DOCTYPE html>
<html>

<body>
    <ul id="fruits">
        <li class="apple">Apple</li>
        <li class="banana">Banana</li>
        <li class="orange">Orange</li>
    </ul>
    <script>
        // 노드 탐색의 기점이 되는 #fruits 요소 노드를 취득한다.
        const $fruits = document.getElementById('fruits');

        // #fruits 요소의 모든 자식 노드를 탐색한다.
        // childNodes 프로퍼티가 반환한 NodeList 에는 요소 노드뿐만 아니라 텍스트 노드도 포함되어 있다.
        console.log($fruits.childNodes);
        // NodeList(7) [text, li.apple, text, li.banana, text, li.orange, text]

        // #fruits 요소의 모든 자식 노드를 탐색한다.
        // children 프로퍼티가 반환한 HTMLCollection에는 요소 노드만 포함되어 있다.
        console.log($fruits.children);
        // HTMLCollection(3) [li.apple, li.banana, li.orange]

        // #fruits 요소의 첫 번째 자식 노드를 탐색한다.
        // firstChild 프로퍼티는 텍스트 노드를 반환할 수도 있다.
        console.log($fruits.firstChild);
        // #text

        // #fruits 요소의 마지막 자식 노드를 탐색한다.
        // lastChild 프로퍼티는 텍스트 노드를 반환할 수도 있다.
        console.log($fruits.lastChild);
        // #text

        // #fruits 요소의 첫 번째 자식 노드를 탐색한다.
        // firstElementChild 프로퍼티는 요소 노드만 반환한다.
        console.log($fruits.firstElementChild);
        // li.apple

        // #fruits 요쇼의 마지막 자식 노드를 탐색한다.
        // lastElementChild 프로퍼티는 요소 노드만 반환한다.
        console.log($fruits.lastElementChild);
        // li.orange
    </script>
</body>

</html>
```

### 39.3.3 자식 노드 존재 확인

Node.prototype.hasChildNodes 메서드는 자식 노드가 존재하면 true, 아니면 false를 반환한다. 텍스트 노드를 포함하여 자식 노드의 존재를 확인한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul id="fruits"></ul>
    </body>
    <script>
        // 노드 탐색의 기점이 되는 #fruits 요소 노드를 취득한다.
        const $fruits = document.getElementById('fruits');

        // #fruits 요소에 자식 노드가 존재하는지 확인한다.
        // hasChildNodes 메소드는 텍스트 노드를 포함하여 자식 노드의 존재를 확인한다.
        console.log($fruits.hasChildNodes()); // true
    </script>
</html>
```

자식 노드 중 텍스트 노드가 아닌 요소 노드가 존재하는지 확인하려면 children.length 또는 Element 인터페이스의 childElementCount 프로퍼티를 사용한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul id="fruits"></ul>
    </body>
    <script>
        // 노드 탐색의 기점이 되는 #fruits 요소 노드를 취득한다.
        const $fruits = document.getElementById('fruits');

        // hasChildNodes 메소드는 텍스트 노드를 포함하여 자식 노드의 존재를 확인한다.
        console.log($fruits.hasChildNodes()); // true

        // 자식 노드 중에 텍스트 노드가 아닌 요소 노드가 존재하는지 확인한다.
        console.log(!!$fruits.children.length); // 0 -> false
        // 자식 노드 중에 텍스트 노드가 아닌 요소 노드가 존재하는지 확인한다.
        console.log(!!$fruits.childElementCount); // 0 -> false
    </script>
</html>
```

### 39.3.4 요소 노드의 텍스트 노드 탐색

요소 노드의 텍스트 노드는 요소 노드의 자식 노드다. 따라서 firstChild 프로퍼티로 접근할 수 있다.

```html
<!DOCTYPE html>
<html>
    <body>
        <div id="foo">Hello</div>
        <script>
            // 요소 노드의 텍스트 노드는 firstChild 프로퍼티로 접근할 수 있다.
            console.log(document.getElementById('foo').firstChild); // #text
        </script>
    </body>
</html>
```

### 39.3.5 부모 노드 탐색

Node.prototype.parentNode 프로퍼티를 사용한다. 텍스트 노드는 DOM 트리의 최종단 노드인 리프 노드이므로 부모 노트가 텍스트 노드인 경우는 없다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul id="fruits">
            <li class="apple">Apple</li>
            <li class="banana">Banana</li>
            <li class="orange">Orange</li>
        </ul>
    </body>
    <script>
        // 노드 탐색의 기점이 되는 .banana 요소 노드를 취득한다.
        const $banana = document.querySelector('.banana');

        // .banana 요소 노드의 부모 노드를 탐색한다.
        console.log($banana.parentNode); // ul#fruits
    </script>
</html>
```

### 39.3.6 형제 노드 탐색

자신의 다음 형제 노드 반환(요소 노드 or 텍스트 노드)

|                   프로퍼티                   |                설명                |
| :--------------------------------------: | :------------------------------: |
|      Node.prototype.previousSibling      | 자신의 이전 형제 노드 반환(요소 노드 or 텍스트 노드) |
|        Node.prototype.nextSibling        | 자신의 다음 형제 노드 반환(요소 노드 or 텍스트 노드) |
| Element.prototype.previousElementSibling |      자신의 이전 형제 노드 반환(요소 노드)      |
|   Element.prototype.nextElementSibling   |      자신의 다음 형제 노드 반환(요소 노드)      |

```html
<!DOCTYPE html>
<html>
    <body>
        <ul id="fruits">
            <li class="apple">Apple</li>
            <li class="banana">Banana</li>
            <li class="orange">Orange</li>
        </ul>
    </body>
    <script>
        // 노드 탐색의 기점이 되는 #fruits 요소 노드를 취득한다.
        const $fruits = document.getElementById('fruits');

        // #fruits 요소의 첫 번째 자식 노드를 탐색한다.
        // firstChild 프로퍼티는 요소 노드뿐만 아니라 텍스트 노드를 반환할 수도 있다.
        const { firstChild } = $fruits;
        console.log(firstChild); // #text

        // #fruits 요소의 첫 번째 자식 노드(텍스트 노드)의 다음 형제 노드를 탐색한다.
        // nextSibling 프로퍼티는 요소 노드뿐만 아니라 텍스트 노드를 반환할 수도 있다.
        const { nextSibling } = firstChild;
        console.log(nextSibling); // li.apple

        // li.apple 요소의 이전 형제 노드를 탐색한다.
        // previousSibling 프로퍼티는 요소 노드뿐만 아니라 텍스트 노드를 반환할 수도 있다.
        const { previousSibling } = nextSibling;
        console.log(previousSibling); // #text

        // #fruits 요소의 첫 번째 자식 노드를 탐색한다.
        // firstElementChild 프로퍼티는 요소 노드만 반환한다.
        const { firstElementChild } = $fruits;
        console.log(firstElementChild); // li.apple

        // #fruits 요소의 첫 번째 자식 요소 노드(li.apple)의 다음 형제 노드를 탐색한다.
        // nextElementSibling 프로퍼티는 요소 노드만 반환한다.
        const { nextElementSibling } = firstElementChild;
        console.log(nextElementSibling); // li.banana

        // li.banana 요소의 이전 형제 노드를 탐색한다.
        // previousElementSibling 프로퍼티는 요소 노드만 반환한다.
        const { previousElementSibling } = nextElementSibling;
        console.log(previousElementSibling); // li.apple

    </script>
</html>
```

## 39.4 노드 정보 취득

노드 객체에 대한 정보를 취득하려면 다음과 같은 노드 정보 프로퍼티를 사용한다.

|           프로퍼티          | 설명                                                                                                                                                                                                         |
| :---------------------: | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Node.prototype.nodeType | <p>노드 객체의 타입을 나타내는 상수를 반환</p><ul><li>Node.ELEMENT_NODE<br>: 요소 노드 타입을 나타내는 상수 1을 반환</li><li>Node.TEXT_NODE<br>: 텍스트 노드 타입을 나타내는 상수 3을 반환</li><li>Node.DOCUMENT_NODE<br>: 문서 노드 타입을 나타내는 상수 9를 반환</li></ul> |
| Node.prototype.nodeName | <p>노드의 이름을 문자열로 반환</p><ul><li>요소 노드<br>: 대문자 문자열로 태그 이름("UL", "LI" 등)을 반환</li><li>텍스트 노드<br>: 문자열 "#text"를 반환</li><li>문서 노드<br>: 문자열 "#document"를 반환</li></ul>                                             |

```html
<!DOCTYPE html>
<html>
    <body>
        <div id="foo">Hello</div>
    </body>
    <script>
        // 문서 노드의 노드 정보를 취득한다.
        console.log(document.nodeType); // 9
        console.log(document.nodeName); // #document

        // 요소 노드의 노드 정보를 취득한다.
        const $foo = document.getElementById('foo');
        console.log($foo.nodeType); // 1
        console.log($foo.nodeName); // DIV

        // 텍스트 노드의 노드 정보를 취득한다.
        const $textNode = $foo.firstChild;
        console.log($textNode.nodeType); // 3
        console.log($textNode.nodeName); // #text
    </script>
</html>
```

## 39.5 요소 노드의 텍스트 조작

### 39.5.1 nodeValue

Node.prototype.nodeValue 프로퍼티는 setter와 getter 모두 존재하는 접근자 프로퍼티다. 따라서 참조와 할당 모두 가능하다.

노드 객체의 nodeValue 프로퍼티를 참조하면 노드 객체의 값을 반환한다. 노드 객체의 값이란 텍스트 노드의 텍스트다.

```html
<!DOCTYPE html>
<html>
    <body>
        <div id="foo">Hello</div>
    </body>
    <script>
        // 문서 노드의 nodeValue 프로퍼티를 참조한다.
        console.log(document.nodeValue); // null

        // 요소 노드의 nodeValue 프로퍼티를 참조한다.
        const $foo = document.getElementById('foo');
        console.log($foo.nodeValue); // null

        // 텍스트 노드의 nodeValue 프로퍼티를 참조한다.
        const $textNode = $foo.firstChild;
        console.log($textNode.nodeValue); // Hello
    </script>
</html>
```

텍스트 노드의 nodeValue 프로퍼티에 값을 할당하면 텍스트 노드의 값, 즉 텍스트를 변경할 수 있다. 따라서 요소 노드의 텍스트를 변경하려면 다음과 같은 순서의 처리가 필요하다.

1. 텍스트를 변경할 요소 노드를 취득한 다음, 취득한 요소 노드의 텍스트 노드를 탐색한다.
2. 탐색한 텍스트 노드의 nodeValue 프로퍼티를 사용하여 텍스트 노드의 값을 변경한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <div id="foo">Hello</div>
    </body>
    <script>
        // 1. #foo 요소 노드의 자식 노드인 텍스트 노드를 취득한다.
        const $textNode = document.getElementById('foo').firstChild;

        // 2. nodeValue 프로퍼티를 사용하여 텍스트 노드의 값을 변경한다.
        $textNode.nodeValue = 'World'

        console.log($textNode.nodeValue); // World
    </script>
</html>
```

### 39.5.2 textContent

Node.prototype.textContent 프로퍼티는 setter와 getter 모두 존재하는 접근자 프로퍼티로서 요소 노드의 텍스트와 모든 자손 노드의 텍스트를 모두 취득하거나 변경한다.

요소 노드의 textContent 프로퍼티를 참조하면 요소 노드의 콘텐츠 영역 내의 텍스트를 모두 반환한다. 다시 말해, 요소 노드의 childNodes 프로퍼티가 반환한 모든 노드들의 텍스트 노드의 값, 즉 텍스트를 모두 반환한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <div id="foo">Hello <span>world!</span></div>
    </body>
    <script>
        // #foo 요소 노도의 텍스트를 모두 취득한다. 이 때 HTML 마크업은 무시된다.
        console.log(document.getElementById('foo').textContent);
        // Hello World
    </script>
</html>
```

요소 노드의 textContent 프로퍼티에 문자열을 할당하면 요소 노드의 모든 자식 노드가 제거되고 할당한 문자열이 텍스트로 추가된다. 이때 할당한 문자열에 HTML 마크업이 포함되어 있더라도 문자열 그대로 인식되어 텍스트로 취급된다. 즉, HTML 마크업이 파싱되지 않는다.

```html
<!DOCTYPE html>
<html>
    <body>
        <div id="foo">Hello <span>world!</span></div>
    </body>
    <script>
        // #foo 요소 노드의 모든 자식 노드가 제거되고 할당한 문자열이 텍스트로 추가된다.
        // 이 때 HTML 마크업이 파싱되지 않는다.
        document.getElementById('foo').textContent = 'Hi <span>there!</span>';
    </script>
</html>
```

참고로 textContent 프로퍼티와 유사한 동작을 하는 innerText 프로퍼티가 있다. 이는 다음과 같은 이유로 사용하지 않는 편이 좋다.

* innerText 프로퍼티는 CSS에 순종적이다. 예를 들어, CSS에 의해 비표시(visibility: hidden;)로 지정된 요소 노드의 텍스트를 반환한지 않는다.
* innerText 프로퍼티는 CSS를 고려해야 하므로 느리다.

## 39.6 DOM 조작

DOM 조작은 새로운 노드를 생성하여 DOM에 추가하거나 기존 노드를 삭제 또는 교체하는 것을 말한다. DOM 조작은 리플로우와 리페인트가 발생하는 원인이 되므로 성능에 영향을 준다. 따라서 복잡한 콘텐츠를 다루는 DOM 조작은 성능 최적화를 위해 주의해서 다루어야 한다.

### 39.6.1 innerHTML

Element.prototype.innerHTML 프로퍼티는 setter와 getter 모두 존재하는 접근자 프로퍼티로서 요소 노드의 HTML 마크업을 취득하거나 변경한다. 요소 노드의 innerHTML 프로퍼티를 참조하면 요소 노드의 콘텐츠 영역 내에 포함된 모든 HTML 마크업을 문자열로 반환한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <div id="foo">Hello <span>world!</span></div>
    </body>
    <script>
        // #foo 요소의 콘텐츠 영역 내의 HTML 마크업을 문자열로 취득한다.
        console.log(document.getElementById('foo').innerHTML);
        // "Hello <span>world!</span>"
    </script>
</html>
```

앞서 살펴본 textContent 프로퍼티를 참조하면 HTML 마크업을 무시하고 텍스트만 반환하지만 innerHTML 프로퍼티는 HTML 마크업이 포함된 문자열을 그대로 반환한다.

요소 노드의 innerHTML 프로퍼티에 문자열을 할당하면 요소 노드의 모든 자식 노드가 제거되고 할당한 문자열에 포함되어 있는 HTML 마크업이 파싱되어 요소 노드의 자식 노드로 DOM에 반영된다.

```html
<!DOCTYPE html>
<html>
    <body>
        <div id="foo">Hello <span>world!</span></div>
    </body>
    <script>
        // HTML 마크업이 파싱되어 요소 노드의 자식 노드로 DOM에 반영된다.
        document.getElementById('foo').innerHTML = 'Hi <span>there!</span>';
    </script>
</html>
```

이처럼 innerHTML 프로퍼티를 사용하면 HTML 마크업 문자열로 간단히 DOM 조작이 가능하다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul id="fruits">
            <li class="apple">Apple</li>
        </ul>
    </body>
    <script>
        const $fruits = document.getElementById('fruits');

        // 노드 추가
        $fruits.innerHTML += '<li class="banana">Banana</li>';
        
        // 노드 교체
        $fruits.innerHTML = '<li class="orange">Orange</li>';

        // 노드 삭제
        $fruits.innerHTML = '';
    </script>
</html>
```

요소 노드의 innerHTML 프로퍼티에 할당한 HTML 마크업 문자열은 렌더링 엔진에 의해 파싱되어 요소 노드의 자식으로 DOM에 반영된다. 이때 사용자로부터 입력받은 데이터(untrusted input data)를 그대로 innerHTML 프로퍼티에 할당하는 것은 <mark style="color:purple;">**크로스 스크립팅 공격(XSS: Cross-site Scripting Attacks)**</mark>에 취약하므로 위험하다. HTML 마크업 내에 자바스크립트 악성 코드가 포함되어 있다면 파싱 과정에서 그대로 실행될 가능성이 있기 때문이다.

innerHTML 프로퍼티의 또 다른 단점은 요소 노드의 innerHTML 프로퍼티에 HTML 마크업 문자열을 할당하는 경우 요소 노드의 모든 자식 노드를 제거하고 할당한 HTML 마크업 문자열을 파싱하여 DOM을 변경한다는 것이다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul id="fruits">
            <li class="apple">Apple</li>
        </ul>
    </body>
    <script>
        const $fruits = document.getElementById('fruits');

        // 노드 추가
        $fruits.innerHTML += '<li class="banana">Banana</li>';
    </script>
</html>
```

위 예제는 #fruits 요소에 자식 요소 li.banana를 추가한다. 이때 #fruits 요소의 자식 요소 li.apple은 아무런 변경이 없으므로 다시 생성할 필요가 없다. 하지만 #fruits의 모든 자식 노드(li.apple)를 제거하고 새롭게 요소 노드 li.apple과 li.banana를 생성하여 #fruits 요소의 자식 요소로 추가한다. 즉, 효율적이지 않다.

또한 innerHTML 프로퍼티는 새로운 요소를 삽입할 때 삽입될 위치를 지정할 수 없다는 단점도 있다.

```html
<ul id="fruits">
    <li class="apple">Apple</li>
    <li class="orange">Orange</li>
</ul>
```

li.apple 요소와 li.orange 요소 사이에 새로운 요소를 삽입하고 싶은 경우 innerHTML 프로퍼티를 사용하면 삽입 위치를 지정할 수 없다.

### 39.6.2 insertAdjacentHTML 메서드

Element.prototype.insertAdjacentHTML(position, DOMString) 메서드는 기존 요소를 제거하지 않으면서 위치를 지정해 새로운 요소를 삽입한다.

두 번째 인수로 전달한 HTML 마크업 문자열(DOMString)을 파싱하고 그 결과로 생성된 노드를 첫 번째 인수로 전달한 위치(position)에 삽입하여 DOM에 반영한다. 첫 번째 인수로 전달할 수 있는 문자열은 'beforebegin', 'afterbegin', 'beforeend', 'afterend'의 4가지다.

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

```html
<!DOCTYPE html>
<html>
    <body>
        <!-- beforebegin -->
        <div id="foo">
        <!-- afterbegin -->
            text
        <!-- beforeend -->
        </div>
        <!-- afterend -->
    </body>
    <script>
        const $foo = document.getElementById('foo');

        $foo.insertAdjacentHTML('beforebegin', '<p>beforebegin</p>');
        $foo.insertAdjacentHTML('afterbegin', '<p>afterbegin</p>');
        $foo.insertAdjacentHTML('beforeend', '<p>beforeend</p>');
        $foo.insertAdjacentHTML('afterend', '<p>afterend</p>');
    </script>
</html>
```

insertAdjacentHTML 메서드는 innerHTML 프로퍼티보다 효율적이고 빠르지만, HTML 마크업 문자열을 파싱하므로 크로스 사이트 스크립팅 공격에 취약하다는 점은 동일하다.

### 39.6.3 노드 생성과 추가

DOM은 노드를 직접 생성/삽입/삭제/치환하는 메서드를 제공한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul id="fruits">
            <li>Apple</li>
        </ul>
    </body>
    <script>
        const $fruits = document.getElementById('fruits');

        // 1. 요소 노드 생성
        const $li = document.createElement('li');

        // 2. 텍스트 노드 생성
        const textNode = document.createTextNode('Banana');

        // 3. 텍스트 노드를 $li 요소 노드의 자식 노드로 추가
        $li.appendChild(textNode);

        // 4. $li 요소 노드를 #fruits 요소 노드의 마지막 자식 노드로 추가
        $fruits.appendChild($li);
    </script>
</html>
```

<mark style="color:green;">**요소 노드 생성**</mark>

Document.prototype.createElement(tagName) 메서드는 요소 노드를 생성하여 반환한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul id="fruits">
            <li>Apple</li>
        </ul>
    </body>
    <script>
        // 1. 요소 노드 생성
        const $li = document.createElement('li');
    </script>
</html>
```

createElement 메서드로 생성한 요소 노드는 기존 DOM에 추가되지 않고 홀로 존재하는 상태다. 따라서 DOM에 추가하는 처리가 별도로 필요하다.

또한 아무런 자식 노드를 가지고 있지 않다. 따라서 요소 노드의 자식 노드인 텍스트 노드도 없는 상태다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul id="fruits">
            <li>Apple</li>
        </ul>
    </body>
    <script>
        // 1. 요소 노드 생성
        const $li = document.createElement('li');
        // 생성된 요소 노드는 아무런 자식 노드가 없다.
        console.log($li.childNodes); // NodeList []
    </script>
</html>
```

<mark style="color:green;">**텍스트 노드 생성**</mark>

Document.prototype.createTextNode(text) 메서드는 텍스트 노드를 생성하여 반환한다.

```javascript
// 2. 텍스트 노드 생성
const textNode = document.createTextNode('Banana');
```

텍스트 노드는 요소 노드의 자식 노드다. 하지만 createTextNode 메서드로 생성한 텍스트 노드는 요소 노드의 자식 노드로 추가되지 않고 홀로 존재하는 상태다. 따라서 이후에 생성된 텍스트 노드를 요소 노드에 추가하는 처리가 별도로 필요하다.

<mark style="color:green;">**텍스트 노드를 요소 노드의 자식 노드로 추가**</mark>

Node.prototype.appendChild(childName) 메서드는 매개변수에 인수로 전달한 노드를 메서드를 호출한 노드의 마지막 자식 노드로 추가한다.

```javascript
// 3. 텍스트 노드를 $li 요소 노드의 자식 노드로 추가
$li.appendChild(textNode);
```

appendChild 메서드를 통해 요소 노드와 텍스트 노드는 부자 관계로 연결되었지만 아직 기존 DOM에 추가되지는 않은 상태다.

<mark style="color:green;">**요소 노드를 DOM에 추가**</mark>

Node.prototype.appendChild 메서드를 사용하여 텍스트 노드와 부자 관계로 연결한 요소 노드를 #fruits 요소 노드의 마지막 자식 요소로 추가한다.

```javascript
// 4. $li 요소 노드를 #fruits 요소 노드의 마지막 자식 노드로 추가
$fruits.appendChild($li);
```

이 과정에서 비로소 새롭게 생성한 요소 노드가 DOM에 추가된다. 단 하나의 요소 노드를 생성하여 DOM에 한 번 추가하므로 DOM은 한 번 변경된다. 이때 리플로우와 리페인트가 실행된다.

### 39.6.4 복수의 노드 생성과 추가

```html
<!DOCTYPE html>
<html>
    <body>
        <ul id="fruits"></ul>
    </body>
    <script>
        const $fruits = document.getElementById('fruits');

        ['Apple', 'Banana', 'Orange'].forEach(text => {
            // 1. 요소 노드 생성
            const $li = document.createElement('li');

            // 2. 텍스트 노드 생성
            const textNode = document.createTextNode(text);

            // 3. 텍스트 노드를 $li 요소 노드의 자식 노드로 추가
            $li.appendChild(textNode);

            // 4. $li 요소 노드를 #fruits 요소 노드의 마지막 자식 노드로 추가
            $fruits.appendChild($li);
        });
    </script>
</html>
```

위 예제는 3개의 요소 노드를 생성하여 DOM에 3번 추가하므로 DOM이 3번 변경된다. 이때 리플로우와 리페인트가 3번 실행된다. 이렇게 기존 DOM에 요소 노드를 반복하여 추가하는 것은 비효율적이다.

이러한 문제를 회피하기 위해 컨테이너 요소를 사용해 보자. 컨테이너 요소를 미리 생성한 다음, DOM에 추가해야 할 3개의 요소 노드를 컨테이너 요소에 자식 노드로 추가하고, 컨테이너 요소를 #fruits 요소에 자식으로 추가한다면 DOM은 한 번만 변경된다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul id="fruits"></ul>
    </body>
    <script>
        const $fruits = document.getElementById('fruits');

        // 컨테이너 요소 노드 생성
        const $container = document.createElement('div');

        ['Apple', 'Banana', 'Orange'].forEach(text => {
            // 1. 요소 노드 생성
            const $li = document.createElement('li');

            // 2. 텍스트 노드 생성
            const textNode = document.createTextNode(text);

            // 3. 텍스트 노드를 $li 요소 노드의 자식 노드로 추가
            $li.appendChild(textNode);

            // 4. $li 요소 노드를 #fruits 요소 노드의 마지막 자식 노드로 추가
            $container.appendChild($li);
        });

        // 5. 컨테이너 요소 노드를 #fruits 요소 노드의 마지막 자식 노드로 추가
        $fruits.appendChild($container);
    </script>
</html>
```

위 예제는 DOM을 한 번만 변경하긴 하지만 불필요한 컨테이너 요소(div)가 DOM에 추가되는 부작용이 있다. 이는 바람직하지 않다.

```html
<ul id="fruits">
    <div>
        <li>apple</li>
        <li>banana</li>
        <li>orange</li>
    </div>
</ul>
```

이러한 문제는 DocumentFragment 노드를 통해 해결할 수 있다. 이는 문서, 요소, 어트리뷰트, 텍스트 노드와 같은 노드 객체의 일종으로, 부모 노드가 없어서 기존 DOM과는 별도로 존재한다는 특징이 있다. DocumentFragment 노드는 위 예제의 컨테이너 요소와 같이 자식 노드들의 부모 노드로서 별도의 서브 DOM을 구성하여 기존 DOM에 추가하기 위한 용도로 사용한다.

DocumentFragment 노드는 기존 DOM과는 별도로 존재하므로 DocumentFragment 노드에 자식 노드를 추가하여도 기존 DOM에는 어떠한 변경도 발생하지 않는다. 또한 DocumentFragment 노드를 DOM에 추가하면 자신은 제거되고 자신의 자식 노드만 DOM에 추가된다.

Document.prototype.createDocumentFragment 메서드는 비어 있는 DocumentFragment 노드를 생성하여 반환한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul id="fruits"></ul>
    </body>
    <script>
        const $fruits = document.getElementById('fruits');

        // DocumentFragment 노드 생성
        const $fragment = document.createDocumentFragment();

        ['Apple', 'Banana', 'Orange'].forEach(text => {
            // 1. 요소 노드 생성
            const $li = document.createElement('li');

            // 2. 텍스트 노드 생성
            const textNode = document.createTextNode(text);

            // 3. 텍스트 노드를 $li 요소 노드의 자식 노드로 추가
            $li.appendChild(textNode);

            // 4. $li 요소 노드를 DocumentFragment 요소 노드의 마지막 자식 노드로 추가
            $fragment.appendChild($li);
        });

        // 5. DocumentFragment 노드를 #fruits 요소 노드의 마지막 자식 노드로 추가
        $fruits.appendChild($fragment);
    </script>
</html>
```

먼저 DocumentFragment 노드를 생성하고 DOM에 추가할 요소 노드를 생성하여 DocumentFragment 노드에 자식 노드로 추가한 다음, DocumentFragment 노드를 기존 DOM에 추가한다.

이때 실제로 DOM 변경이 발생하는 것은 한 번뿐이며 리플로우와 리페인트도 한 번만 실행된다. 따라서 여러 개의 요소 노드를 DOM에 추가하는 경우 DocumentFragment 노드를 사용하는 것이 더 효율적이다.

### 39.6.5 노드 삽입

<mark style="color:green;">**마지막 노드로 추가**</mark>

Node.prototype.appendChild 메서드는 인수로 전달받은 노드를 자신을 호출한 노드의 마지막 자식 노드로 DOM에 추가한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul id="fruits">
            <li>Apple</li>
            <li>Banana</li>
        </ul>
    </body>
    <script>
        // 요소 노드 생성
        const $li = document.createElement('li');

        // 텍스트 노드를 $li 요소 노드의 마지막 자식 노드로 추가
        $li.appendChild(document.createTextNode('Orange'));

        // $li 요소 노드를 #fruits 요소 노드의 마지막 자식 노드로 추가
        document.getElementById('fruits').appendChild($li);
    </script>
</html>
```

<mark style="color:green;">**지정한 위치에 노드 삽입**</mark>

Node.prototype.insertBefore(newNode, childNode) 메서드는 첫 번째 인수로 전달받은 노드를 두 번째 인수로 전달받은 노드 앞에 삽입한다.

두 번째 인수로 전달받은 노드는 반드시 insertBefore 메서드를 호출한 노드의 자식 노드이어야 한다.

두 번째 인수로 전달받은 노드가 null이면 첫 번째 인수로 전달받은 노드를 insertBefore 메서드를 호출한 노드의 마지막 자식 노드로 추가한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul id="fruits">
            <li>Apple</li>
            <li>Banana</li>
        </ul>
    </body>
    <script>
        const $fruits = document.getElementById('fruits');

        // 요소 노드 생성
        const $li = document.createElement('li');

        // 텍스트 노드를 $li 요소 노드의 마지막 자식 노드로 추가
        $li.appendChild(document.createTextNode('Orange'));

        // $li 요소 노드를 #fruits 요소 노드의 마지막 자식 요소 앞에 삽입
        $fruits.insertBefore($li, $fruits.lastElementChild);
        // Apple - Orange - Banana
    </script>
</html>
```

### 39.6.6 노드 이동

DOM에 이미 존재하는 노드를 appendChild 또는 insertBefore 메서드를 사용하여 DOM에 다시 추가하면 현재 위치에서 노드를 제거하고 새로운 위치에 노드를 추가한다. 즉, 노드가 이동한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul id="fruits">
            <li>Apple</li>
            <li>Banana</li>
            <li>Orange</li>
        </ul>
    </body>
    <script>
        const $fruits = document.getElementById('fruits');

        // 이미 존재하는 요소 노드를 취득
        const [$apple, $banana, ] = $fruits.children;

        // 이미 존재하는 $apple 요소 노드를 #fruits 요소 노드의 마지막 노드로 이동
        $fruits.appendChild($apple);
        // Banana - Orange - Apple

        // 이미 존재하는 $banana 요소 노드를 #fruits 요소의 마지막 노드 앞으로 이동
        $fruits.insertBefore($banana, $fruits.lastElementChild);
        // Orange - Banana - Apple
    </script>
</html>
```

### 39.6.7 노드 복사

Node.prototype.cloneNode(\[deep: true | false]) 메서드는 노드의 사본을 생성하여 반환한다.

매개변수 deep에

* true를 전달: 노드를 깊은 복사하여 모든 자손 노드가 포함된 사본 생성
* false를 전달: 노드를 얕은 복사하여 노드 자신만의 사본을 생성. 자손 노드를 복사하지 않으므로 텍스트 노드도 없다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul id="fruits">
            <li>Apple</li>
        </ul>
    </body>
    <script>
        const $fruits = document.getElementById('fruits');
        const $apple = $fruits.firstElementChild;

        // $apple 요소를 얕은 복사하여 사본을 생성. 텍스트 노드가 없는 사본이 생성된다.
        const $shallowClone = $apple.cloneNode();

        // 사본 요소 노드에 텍스트 추가
        $shallowClone.textContent = 'Banana';

        // 사본 요소 노드를 #fruits 요소 노드의 마지막 노드로 추가
        $fruits.appendChild($shallowClone);

        // #fruits 요소를 깊은 복사하여 모든 자손 노드가 포함된 사본을 생성
        const $deepClone = $fruits.cloneNode(true);

        // 사본 요소 노드를 #fruits 요소 노드의 마지막 노드로 추가
        $fruits.appendChild($deepClone);
    </script>
</html>
```

### 39.6.8 노드 교체

Node.prototype.replaceChild(newChild, oldChild) 메서드는 자신을 호출한 노드의 자식 노드를 다른 노드로 교체한다.

oldChild 매개변수에 인수로 전달한 노드는 replaceChild 메서드를 호출한 노드의 자식 노드이어야 한다.

즉, replaceChild 메서드는 자신을 호출한 노드의 자식 노드인 oldChild 노드를 newChild 노드로 교체한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul id="fruits">
            <li>Apple</li>
        </ul>
    </body>
    <script>
        const $fruits = document.getElementById('fruits');
        
        // 기존 노드와 교체할 요소 노드를 생성
        const $newChild = document.createElement('li');
        $newChild.textContent = 'Banana';

        // #fruits 요소 노드의 첫 번째 자식 요소 노드를 $newChild 요소 노드로 교체
        $fruits.replaceChild($newChild, $fruits.firstElementChild);
    </script>
</html>
```

### 39.6.9 노드 삭제

Node.prototype.removeChild(child) 메서드는 child 매개변수에 인수로 전달한 노드를 DOM에서 삭제한다. 인수로 전달한 노드는 removeChild 메서드를 호출한 노드의 자식 노드이어야 한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul id="fruits">
            <li>Apple</li>
            <li>Banana</li>
        </ul>
    </body>
    <script>
        const $fruits = document.getElementById('fruits');

        // #fruits 요소 노드의 마지막 요소를 DOM에서 삭제
        $fruits.removeChild($fruits.lastElementChild);
    </script>
</html>
```

## 39.7 어트리뷰트

### 39.7.1 어트리뷰트 노드와 attributes 프로퍼티

HTML 문서의 구성 요소인 HTML 요소는 여러 개의 어트리뷰트(속성)를 가질 수 있다.

```html
<input id="user" type="text" value="kkw">
```

* 글로벌 어트리뷰트: id, class, style, title, lang, tabindex, draggable, hidden 등
* 이벤트 핸들러 어트리뷰트: onclick, onchange, onfocus, onblur, oninput, onkeypress, onkeyup, onmouseover, onsubmit, onload 등

위 두 어트리뷰트는 모든 HTML 요소에서 공통적으로 사용할 수 있지만 특정 HTML 요소에만 한정적으로 사용 가능한 어트리뷰트도 있다. type, value, checked 어트리뷰트는 input 요소에만 사용할 수 있는 것처럼.

HTML 문서가 파싱될 때 HTML 요소의 어트리뷰트는 어트리뷰트 노드로 변환되어 요소 노드와 연결된다. 이때 HTML 어트리뷰트당 하나의 어트리뷰트 노드가 생성된다. 즉, 위 input 요소는 3개의 어트리뷰트가 있으므로 3개의 어트리뷰트 노드가 생성된다.

이때 모든 어트리뷰트 노드의 참조는 유사 배열 객체이자 이터러블인 NamedNodeMap 객체에 감겨서 요소 노드의 attributes 프로퍼티에 저장된다.

따라서 요소 노드의 모든 어트리뷰트 노드는 요소 노드의 Element.prototype.attributes 프로퍼티로 취득할 수 있다. attributes 프로퍼티는 getter만 존재하는 읽기 전용 접근자 프로퍼티이며, 요소 노드의 모든 어트리뷰트 노드의 참조가 담긴 NamedNodeMap 객체를 반환한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <input id="user" type="text" value="ungmo2">
        <script>
            // 요소 노드의 attribute 프로퍼티는 요소 노드의 모든 어트리뷰트 노드의 참조가 담긴
            // NamedNodeMap 객체를 반환한다.
            const { attributes } = document.getElementById('user');
            console.log(attributes);
            // NamedNodeMap {0: id, 1: type, 2: value, id: id, type: type, value: value, length: 3}

            // 어트리뷰트 값 취득
            console.log(attributes.id.value); // user
            console.log(attributes.type.value); // text
            console.log(attributes.value.value); // kkw
        </script>
    </body>
</html>
```

### 39.7.2 HTML 어트리뷰트 조작

HTML 어트리뷰트 값을 참조하려면 Element.prototype.getAttribute(attributeName) 메서드를, HTML 어트리뷰트 값을 변경하려면 Element.prototype.setAttribute(attributeName, attributeValue) 메서드를 사용한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <input id="user" type="text" value="kkw">
        <script>
            const $input = document.getElementById('user');

            // value 어트리뷰트 값을 취득
            const inputValue = $input.getAttribute('value');
            console.log(inputValue); // kkw

            // value 어트리뷰트 값을 변경
            $input.setAttribute('value', 'foo');
            console.log($input.getAttribute('value')); // foo
        </script>
    </body>
</html>
```

특정 HTML 어트리뷰트가 존재하는지 확인하려면 Element.prototype.hasAttribute(attributeName) 메서드를 사용하고, 특정 HTML 어트리뷰트를 삭제하려면 Element.prototype.removeAttribute(attributeName) 메서드를 사용한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <input id="user" type="text" value="ungmo2">
        <script>
            const $input = document.getElementById('user');

            // value 어트리뷰트의 존재 확인
            if ($input.hasAttribute('value')) {
                // value 어트리뷰트 삭제
                $input.removeAttribute('value');
            }

            // value 어트리뷰트가 삭제되었다.
            console.log($input.hasAttribute('value')); // false
        </script>
    </body>
</html>
```

### 39.7.3 HTML 어트리뷰트 vs DOM 프로퍼티

요소 노드 객체에는 HTML 어트리뷰트에 대응하는 프로퍼티, 즉 DOM 프로퍼티가 존재한다. 이 DOM 프로퍼티들은 HTML 어트리뷰트 값을 초기값으로 가지고 있다.

예를 들어, \<input id="user" type="text" value="kkw" /> 요소가 파싱되어 생성된 요소 노드 객체에는 id, type, value 어트리뷰트에 대응하는 id, type, value 프로퍼티가 존재하며, 이 DOM 프로퍼티들은 HTML 어트리뷰트의 값을 초기값으로 가지고 있다.

DOM 프로퍼티는 setter와 getter 모두 존재하는 접근자 프로퍼티다. 따라서 DOM 프로퍼티는 참조와 변경이 가능하다.

```html
<!DOCTYPE html>
<html>
    <body>
        <input id="user" type="text" value="ungmo2">
        <script>
            const $input = document.getElementById('user');

            // 요소 노드의 value 프로퍼티 값을 변경
            $input.value = 'foo';

            // 요소 노드의 value 프로퍼티 값을 참조
            console.log($input.value); // foo
        </script>
    </body>
</html>
```

이처럼 HTML 어트리뷰트는 다음과 같이 DOM에서 중복 관리되고 있는 것처럼 보인다.

1. 요소 노드의 attributes 프로퍼티에서 관리하는 어트리뷰트 노드
2. HTML 어트리뷰트에 대응하는 요소 노드의 프로퍼티(DOM 프로퍼티)

HTML 어트리뷰트는 DOM에서 중복 관리되고 있을까? 그렇지 않다. 우선 HTML 어트리뷰트의 역할을 살펴보자.

<mark style="color:purple;">**HTML 어트리뷰트의 역할은 HTML 요소의 초기 상태를 지정하는 것이다. 즉, HTML 어트리뷰트 값은 HTML 요소의 초기 상태를 의미하며 이는 변하지 않는다.**</mark>

```html
<!DOCTYPE html>
<html>
    <body>
        <input id="user" type="text" value="kkw">
        <script>
            const $input = document.getElementById('user');

            // attribute 프로퍼티에 저장된 value 어트리뷰트 값
            console.log($input.getAttribute('value')); // kkw

            // 요소 노드의 value 프로퍼티에 저장된 value 어트리뷰트 값
            console.log($input.value); // kkw
        </script>
    </body>
</html>
```

하지만 첫 렌더링 이후 사용자가 input 요소에 무언가를 입력하기 시작하면 상황이 달라진다.

<mark style="color:purple;">**요소 노드는 상태(state)를 가지고 있다.**</mark> 예를 들어, input 요소 노드는 사용자가 입력 필드에 입력한 값을 상태로 가지고 있으며, checkbox 요소 노드는 사용자가 입력 필드에 입력한 체크 여부를 상태로 가지고 있다. input 요소 노드나 checkbox 요소 노드가 가지고 있는 상태는 사용자의 입력에 의해 변화하는, 살아있는 것이다.

사용자가 input 요소의 입력 필드에 "foo"라는 값을 입력한 경우를 생각해보자. 이때 input 요소 노드는 사용자의 입력에 의해 변경된 <mark style="color:purple;">**최신 상태("foo")**</mark>를 관리해야 하는 것은 물론, HTML 어트리뷰트로 지정한 <mark style="color:purple;">**초기 상태("kkw")**</mark>도 관리해야 한다. 초기 상태 값을 관리하지 않으면 웹페이지를 처음 표시하거나 새로고침할 때 초기 상태를 표시할 수 없다.

이처럼 <mark style="color:purple;">**요소 노드는 2개의 상태, 즉 초기 상태와 최신 상태를 관리해야 한다. 요소 노드의 초기 상태는 어트리뷰트 노드가 관리하며, 요소 노드의 최신 상태는 DOM 프로퍼티가 관리한다.**</mark>

<mark style="color:green;">**어트리뷰트 노드**</mark>

<mark style="color:purple;">**HTML 어트리뷰트로 지정한 HTML 요소의 초기 상태는 어트리뷰트 노드에서 관리한다.**</mark> 어트리뷰트 노드에서 관리하는 어트리뷰트 값은 사용자의 입력에 의해 상태가 변경되어도 변하지 않고 HTML 어트리뷰트로 지정한 HTML 요소의 초기 상태를 그대로 유지한다.

어트리뷰트 노드가 관리하는 초기 상태 값을 취득하거나 변경하려면 getAttribute/setAttribute 메서드를 사용한다.

getAttribute 메서드로 취득한 값은 어트리뷰트 노드에서 관리하는 HTML 요소에 지정한 어트리뷰트 값, 즉 초기 상태 값이다. HTML 요소에 지정한 어트리뷰트 값은 사용자의 입력에 의해 변하지 않으므로 결과는 언제나 동일하다.

```html
<!DOCTYPE html>
<html>
    <body>
        <input id="user" type="text" value="kkw">
        <script>
            // attributes 프로퍼티에 저장된 value 어트리뷰트 값을 취득한다. 결과는 언제나 동일하다.
            document.getElementById('user').getAttribute('value'); // kkw
        </script>
    </body>
</html>
```

setAttribute 메서드는 어트리뷰트 노드에서 관리하는 HTML 요소에 지정한 어트리뷰트 값, 즉 초기 상태 값을 변경한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <input id="user" type="text" value="kkw">
        <script>
            // HTML 요소에 지정한 어트리뷰트 값, 즉 초기 상태 값을 변경한다.
            document.getElementById('user').setAttribute('value', 'foo');
        </script>
    </body>
</html>
```

<mark style="color:green;">**DOM 프로퍼티**</mark>

<mark style="color:purple;">**사용자가 입력한 최신 상태는 HTML 어트리뷰트에 대응하는 요소 노드의 DOM 프로퍼티가 관리한다. DOM 프로퍼티는 사용자의 입력에 의한 상태 변화에 반응하여 언제나 최신 상태를 유지한다.**</mark>

DOM 프로퍼티로 취득한 값은 HTML 요소의 최신 상태 값을 의미한다. 이 최신 상태 값은 사용자의 입력에 의해 언제든지 동적으로 변경되어 최신 상태를 유지한다. 이에 반해, getAttribute 메서드로 취득한 HTML 어트리뷰트 값, 즉 초기 상태 값은 변하지 않고 유지된다.

```html
<!DOCTYPE html>
<html>
    <body>
        <input id="user" type="text" value="kkw">
        <script>
            const $input = document.getElementById('user');

            // 사용자가 input 요소의 입력 필드에 값을 입력할 때마다 input 요소 노드의 value 프로퍼티 값,
            // 즉 최신 상태 값을 취득한다. value 프로퍼티 값은 사용자의 입력에 의해 동적으로 변경된다.
            $input.oninput = () => {
                console.log('value 프로퍼티 값', $input.value);
            };

            // getAttribute 메소드로 취득한 HTML 어트리뷰트 값, 즉 초기 상태 값은 변하지 않고 유지된다.
            console.log('value 어트리뷰트 값', $input.getAttribute('value'));
        </script>
    </body>
</html>
```

DOM 프로퍼티에 값을 할당하는 것은 HTML 요소의 최신 상태 값을 변경하는 것을 의미한다. 즉, 사용자가 상태를 변경하는 행위와 같다. 이때 HTML 요소에 지정한 어트리뷰트 값에는 어떠한 영향도 주지 않는다.

```html
<!DOCTYPE html>
<html>
    <body>
        <input id="user" type="text" value="kkw">
        <script>
            const $input = document.getElementById('user');

            // DOM 프로퍼티에 값을 할당하여 HTML 요소의 최신 상태를 변경한다.
            $input.value = 'foo';
            console.log($input.value); // foo

            // getAttribute 메소드로 취득한 HTML 어트리뷰트 값, 즉 초기 상태 값은 변하지 않고 유지된다.
            console.log('value 어트리뷰트 값', $input.getAttribute('value')); // kkw
        </script>
    </body>
</html>
```

이처럼 HTML 어트리뷰트는 HTML 요소의 초기 상태 값을 관리하고, DOM 프로퍼티는 사용자의 입력에 의해 변경되는 최신 상태를 관리한다.

<mark style="color:green;">**HTML 어트리뷰트와 DOM 프로퍼티의 대응 관계**</mark>

대부분의 HTML 어트리뷰트는 HTML 어트리뷰트 이름과 동일한 DOM 프로퍼티와 1:1로 대응한다.

* id 어트리뷰트와 id 프로퍼티는 1:1 대응하며, 동일한 값으로 연동한다.
* input 요소의 value 어트리뷰트는 value 프로퍼티와 1:1 대응한다. 하지만 value 어트리뷰트는 초기 상태를, value 프로퍼티는 최신 상태를 갖는다.
* class 어트리뷰트는 className, classList 프로퍼티와 대응한다.
* for 어트리뷰트는 htmlFor 프로퍼티와 1:1 대응한다.
* td 요소의 colspan 어트리뷰트는 대응하는 프로퍼티가 존재하지 않는다.
* textContent 프로퍼티는 대응하는 어트리뷰트가 존재하지 않는다.
* 어트리뷰트 이름은 대소문자를 구별하지 않지만 대응하는 프로퍼티 키는 카멜 케이스를 따른다.

<mark style="color:green;">**DOM 프로퍼티 값의 타입**</mark>

getAttribute 메서드로 취득한 어트리뷰트 값은 언제나 문자열이다. 하지만 DOM 프로퍼티로 취득한 최신 상태 값은 문자열이 아닐 수도 있다.

```html
<!DOCTYPE html>
<html>
    <body>
        <input type="checkbox" checked>
        <script>
            const $checkbox = document.querySelector('input[type=checkbox');

            // getAttribute 메소드로 취득한 어트리뷰트 값은 언제나 문자열이다.
            console.log($checkbox.getAttribute('checked')); // ''

            // DOM 프로퍼티로 취득한 최신 상태 값은 문자열이 아닐 수도 있다.
            console.log($checkbox.checked); // true
        </script>
    </body>
</html>
```

### 39.7.4 data 어트리뷰트와 dataset 프로퍼티

data 어트리뷰트와 dataset 프로퍼티를 사용하면 HTML 요소에 정의한 사용자 정의 어트리뷰트와 자바스크립트 간에 데이터를 교환할 수 있다.

data 어트리뷰트는 data-user-id, data-role과 같이 data- 접두사 다음에 임의의 이름을 붙여 사용한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul class="users">
            <li id="1" data-user-id="7621" data-role="admin">Lee</li>
            <li id="2" data-user-id="9524" data-role="subscriber">Kim</li>
        </ul>
    </body>
</html>
```

data 어트리뷰트의 값은 HTMLElement.dataset 프로퍼티로 취득할 수 있다. dataset 프로퍼티는 HTML 요소의 모든 data 어트리뷰트의 정보를 제공하는 DOMStringMap 객체를 반환한다.

DOMStringMap 객체는 data 어트리뷰트의 data- 접두사 다음에 붙인 임의의 이름을 카멜 케이스로 변환한 프로퍼티를 가지고 있다. 이 프로퍼티로 data 어트리뷰트의 값을 취득하거나 변경할 수 있다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul class="users">
            <li id="1" data-user-id="7621" data-role="admin">Lee</li>
            <li id="2" data-user-id="9524" data-role="subscriber">Kim</li>
        </ul>
        <script>
            const users = [...document.querySelector('.users').children];

            // user-id가 '7621'인 요소 노드를 취득한다.
            const user = users.find(user => user.dataset.userId === '7621');
            // user-id가 '7621'인 요소 노드에서 data-role의 값을 취득한다.
            console.log(user.dataset.role); // "admin"

            // user-id가 '7621'인 요소 노드의 data-role 값을 변경한다.
            user.dataset.role = 'subscriber';
            // dataset 프로퍼티는 DOMStringMap 객체를 반환한다.
            console.log(user.dataset); // DOMStringMap {userId: "7621", role: "subscriber"}
        </script>
    </body>
</html>
```

data 어트리뷰트의 data- 접두사 다음에 존재하지 않는 이름을 키로 사용하여 dataset 프로퍼티에 값을 할당하면 HTML 요소에 data 어트리뷰트가 추가된다.

```html
<!DOCTYPE html>
<html>
    <body>
        <ul class="users">
            <li id="1" data-user-id="7621">Lee</li>
            <li id="2" data-user-id="9524">Kim</li>
        </ul>
        <script>
            const users = [...document.querySelector('.users').children];

            // user-id가 '7621'인 요소 노드를 취득한다.
            const user = users.find(user => user.dataset.userId === '7621');

            // user-id가 '7621'인 요소 노드에 새로운 data 어트리뷰트를 추가한다.
            user.dataset.role = 'admin';
            console.log(user.dataset);
            // DOMStringMap {userId: '7621', role: 'admin'}
            // <li id="1" data-user-id="7621" data-role="admin">Lee</li>
        </script>
    </body>
</html>
```

## 39.8 스타일

### 39.8.1 인라인 스타일 조작

HTMLElement.prototype.style 프로퍼티는 setter와 getter 모두 존재하는 접근자 프로퍼티로서 <mark style="color:purple;">**요소 노드의 인라인 스타일을 취득하거나 추가 또는 변경**</mark>한다.

```html
<!DOCTYPE html>
<html>
    <body>
        <div style="color: red">Hello World</div>
        <script>
            const $div = document.querySelector('div');

            // 인라인 스타일 취득
            console.log($div.style); // CSSStyleDeclaration { 0: "color", ... }

            // 인라인 스타일 변경
            $div.style.color = 'blue';

            // 인라인 스타일 추가
            $div.style.width = '100px';
            $div.style.height = '100px';
            $div.style.backgroundColor = 'yellow';
        </script>
    </body>
</html>
```

style 프로퍼티를 참조하면 CSSStyleDeclaration 타입의 객체를 반환한다. CSSStyleDeclaration 객체는 다양한 CSS 프로퍼티에 대응하는 프로퍼티를 가지고 있으며, 이 프로퍼티에 값을 할당하면 해당 CSS 프로퍼티가 인라인 스타일로 HTML 요소에 추가되거나 변경된다.

CSS 프로퍼티는 케밥 케이스를 따른다. 이에 대응하는 CSSStyleDeclaration 객체의 프로퍼티는 카멜 케이스를 따른다.

```html
$div.style.backgroundColor = 'yellow';
```

케밥 케이스의 CSS 프로퍼티를 그대로 사용하려면 객체의 마침표 표기법 대신 대괄호 표기법을 사용한다.

```html
$div.style['background-color'] = 'yellow';
```

단위 지정이 필요한 CSS 프로퍼티의 값은 반드시 단위를 지정해야 한다.

```html
$div.style.width = '100px';
```

### 39.8.2 클래스 조작

.으로 시작하는 클래스 선택자를 사용하여 CSS class를 미리 정의한 다음, HTML 요소의 class 어트리뷰트 값을 변경하여 HTML 요소의 스타일을 변경할 수도 있다.

이때 HTML 요소의 class 어트리뷰트를 조작하려면 class 어트리뷰트에 대응하는 요소 노드의 DOM 프로퍼티를 사용한다.

단, class 어트리뷰트에 대응하는 DOM 프로퍼티는 class가 아니라 className과 classList다.

<mark style="color:green;">**className**</mark>

Element.prototype.className 프로퍼티는 setter와 getter 모두 존재하는 접근자 프로퍼티로서 HTML 요소의 class 어트리뷰트 값을 취득하거나 변경한다.

요소 노드의 className 프로퍼티를 참조하면 class 어트리뷰트 값을 문자열로 반환하고, 요소 노드의 className 프로퍼티에 문자열을 할당하면 class 어트리뷰트 값을 할당한 문자열로 변경한다.

```html
<!DOCTYPE html>
<html>
    <head>
        <style>
            .box {
                width: 100px;
                height: 100px;
                background-color: antiquewhite;
            }
            .red { color: red; }
            .blue { color: blue; }
        </style>
    </head>
    <body>
        <div class="box red">Hello World</div>
        <script>
            const $box = document.querySelector('.box');

            // .box 요소의 class 어트리뷰트 값을 취득
            console.log($box.className); // 'box red'

            // .box 요소의 class 어트리뷰트 값 중에서 'red'만 'blue'로 변경
            $box.className = $box.className.replace('red', 'blue');
        </script>
    </body>
</html>
```

<mark style="color:green;">**classList**</mark>

Element.prototype.classList 프로퍼티는 class 어트리뷰트의 정보를 담은 DOMTokenList 객체를 반환한다.

```html
<!DOCTYPE html>
<html>
    <head>
        <style>
            .box {
                width: 100px;
                height: 100px;
                background-color: antiquewhite;
            }
            .red { color: red; }
            .blue { color: blue; }
        </style>
    </head>
    <body>
        <div class="box red">Hello World</div>
        <script>
            const $box = document.querySelector('.box');

            // .box 요소의 class 어트리뷰트 정보를 담은 DOMTokenList 객체를 취득
            // classList가 반환하는 DOMTokenList 객체는 HTMLCollection과 NodeList와 같이
            // 노드 객체의 상태 변화를 실시간으로 반영하는 살아 있는 객체다.
            console.log($box.classList);
            // DOMTokenList(2) [length: 2, value: "box blue", 0: "box", 1: "blue"]

            // .box 요소의 class 어트리뷰트 값 중에서 'red'만 'blue'로 변경
            $box.classList.replace('red', 'blue');
        </script>
    </body>
</html>
```

DOMTokenList 객체는 class 어트리뷰트의 정보를 나타내는 컬렉션 객체로서 유사 배열 객체이면서 이터러블이다. 다음과 같은 유용한 메서드들을 제공한다.

* add(...className)\
  인수로 전달한 1개 이상의 문자열을 class 어트리뷰트 값으로 추가한다.

```html
$box.classList.add('foo'); // class="box red foo"
$box.classList.add('bar', 'baz'); // class="box red foo bar baz"
```

* remove(...className)\
  인수로 전달한 1개 이상의 문자열과 일치하는 클래스를 class 어트리뷰트에서 삭제한다.

```html
$box.classList.remove('foo'); // class="box red bar baz"
$box.classList.remove('bar', 'baz'); // class="box red"
$box.classList.remove('x'); // class="box red"
```

* item(index)\
  인수로 전달한 index에 해당하는 클래스를 class 어트리뷰트에서 반환한다.

```html
$box.classList.item(0); // "box"
$box.classList.item(1); // "red"
```

* contains(className)\
  인수로 전달한 문자열과 일치하는 클래스가 class 어트리뷰트에 포함되어 있는지 확인한다.

```markup
$box.classList.contains('box');  // true
$box.classList.contains('blue'); // false
```

* replace(oldClassName, newClassName)\
  class 어트리뷰트에서 첫 번째 인수로 전달한 문자열을 두 번째 인수로 전달한 문자열로 변경한다.

```html
$box.classList.replace('red', 'blue'); // class="box blue"
```

* toggle(className\[, force])\
  class 어트리뷰트에 인수로 전달한 문자열과 일치하는 클래스가 존재하면 제거하고, 존재하지 않으면 추가한다.\
  두 번째 인수로 불리언 값으로 평가되는 조건식을 전달할 수 있다.

```
$box.classList.toggle('foo'); // class="box blue foo"
$box.classList.toggle('foo'); // class="box blue"
```

```html
// class 어트리뷰트에 강제로 'foo' 클래스를 추가
$box.classList.toggle('foo', true);  // class="box blue foo"
// class 어트리뷰트에서 강제로 'foo' 클래스를 제거
$box.classList.toggle('foo', false); // class="box blue"
```

이 밖에도 forEach, entries, keys, values, supports 메서드를 제공한다.

### 39.8.3 요소에 적용되어 있는 CSS 스타일 참조

style 프로퍼티는 인라인 스타일만 반환한다. 따라서 클래스를 적용한 스타일이나 상속을 통해 암묵적으로 적용된 스타일은 style 프로퍼티로 참조할 수 없다. HTML 요소에 적용되어 있는 모든 CSS 스타일을 참조해야 할 경우 getComputedStyle 메서드를 사용한다.

window.getComputedStyle(element\[, pseudo]) 메서드는 첫 번째 인수로 전달한 요소 노드에 적용되어 있는 평가된 스타일을 CSSStyleDeclaration 객체에 담아 반환한다.

평가된 스타일(computed style)이란 요소 노드에 적용되어 있는 모든 스타일, 즉 링크 스타일, 임베딩 스타일, 인라인 스타일, 자바스크립트에서 적용한 스타일, 상속된 스타일, 기본(user agent) 스타일 등 모든 스타일이 조합되어 최종적으로 적용된 스타일을 말한다.

```html
<!DOCTYPE html>
<html>
    <head>
        <style>
            body {
                color: red;
            }
            .box {
                width: 100px;
                height: 50px;
                background-color: cornsilk;
                border: 1px solid black;
            }
        </style>
    </head>
    <body>
        <div class="box">Box</div>
        <script>
            const $box = document.querySelector('.box');

            // .box 요소에 적용된 모든 CSS 스타일을 담고 있는 CSSStyleDeclaration 객체를 취득
            const computedStyle = window.getComputedStyle($box);
            console.log(computedStyle); // CSSStyleDeclaration

            // 임베딩 스타일
            console.log(computedStyle.width); // 100px
            console.log(computedStyle.height); // 50px
            console.log(computedStyle.backgroundColor); // rgb(255, 248, 220)
            console.log(computedStyle.border); // 1px solid rgb(0, 0, 0)

            // 상속 스타일(body -> .box)
            console.log(computedStyle.color); // rgb(255, 0, 0)

            // 기본 스타일
            console.log(computedStyle.display); // block
        </script>
    </body>
</html>
```

getComputedStyle 메서드의 두 번째 인수(pseudo)로 :after, :before와 같은 의사 요소를 지정하는 문자열을 전달할 수 있다. 의사 요소가 아닌 일반 요소의 경우 두 번째 인수는 생략한다.

```html
<!DOCTYPE html>
<html>
    <head>
        <style>
            .box:before {
                content: 'Hello'
            }
        </style>
    </head>
    <body>
        <div class="box">Box</div>
        <script>
            const $box = document.querySelector('.box');

            // 의사 요소 :before의 스타일을 취득한다.
            const computedStyle = window.getComputedStyle($box, ':before');
            console.log(computedStyle.content); // "Hello"
        </script>
    </body>
</html>
```

> Reference
>
> 모던 자바스크립트 DEEP DIVE
>
> **2023.03.09**
