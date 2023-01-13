# 01-JSX

```javascript
const element = <h1>Hello. world!</h1>;
```

변수에 html 태그를 담는듯한 이 문법은 `JSX`라 부르며, 이는 `javascript 확장 문법`이다. javascript의 모든 기능이 포함되어 있다는 뜻. 한 마디로 React 전용 html.

JSX는 리액트 `엘리먼트(element)`를 생성한다.\
(엘리먼트란 리액트 앱의 가장 작은 단위. 화면에 표시할 내용)

***

### JSX란? <a href="#jsx" id="jsx"></a>

리액트에서는 렌더링 로직이 UI 로직(이벤트 핸들링, state 변화 등)과 결합된다.

markup(html)과 로직(js)을 별도의 파일에 넣어 분리하는 대신, 리액트에서는 두 가지 모두를 포함하는 `컴포넌트`를 생성할 수 있다.

JSX는 단지 자바스크립트 확장 문법일뿐 필수는 아니다. 다만 JSX를 사용하면 `매우 편하게 UI를 렌더링`할 수 있다.

***

### JSX에 표현식 포함하기 <a href="#jsx" id="jsx"></a>

```javascript
const name = 'Cesc Fabregas';
const element = <h1>Hello, {name}</h1>;
```

name 변수를 선언한 후 중괄호로 감싸 JSX 내부에서 사용했다.

JSX 내부 중괄호 안에는 `유효한(any valid) 자바스크립트 표현식`을 넣을 수 있다. 2 + 2, user.firstName, formatName(user) 등 모두 유효한 자바스크립트 표현식이다.

(표현식이란 값으로 평가될 수 있는 문(statement))

```javascript
const user = {
  firstName: 'Cesc',
  lastName: 'Fabregas'
}
  
const formatName(user) {
  return user.firstName + ' ' + user.lastName;
}

const element = (
  <h1>
  	Hello, {formatName(user)}!
  </h1>
);
```

***

### JSX 자체도 표현식 <a href="#jsx" id="jsx"></a>

컴파일이 끝나면 JSX 표현식은 하나의 자바스크립트 함수가 되어 호출되고, 자바스크립트에서 함수는 객체이다.

즉, JSX를 if 구문 및 for 문 내에서 사용할 수 있고, 변수에 할당할 수 있으며, 인자로 받을 수 있고, 함수의 return 값이 될 수도 있다.

```javascript
const getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}
```

***

### JSX를 사용한 속성 지정 <a href="#jsx" id="jsx"></a>

중괄호를 사용하여 자바스크립트 표현식을 감싸 속성에 사용할 수도 있다.

```javascript
const element = <img src={user.avatarUrl}></img>;
```

속성에 따옴표로 된 string과 중괄호로 감싼 표현식 모두 사용할 수 있지만 한 속성에 같이 사용할 순 없다.

> JSX는 HTML 보단 자바스크립트에 가깝기 때문에 React DOM은 프로퍼티 네이밍 컨벤션에 HTML 어트리뷰트 이름 대신 camelCase를 사용한다.\
> ex. class -> className, tabindex -> tabIndex

***

### JSX로 children 지정 <a href="#jsx-children" id="jsx-children"></a>

태그가 비어있다면 `/>`를 이용해 닫아줄 수 있다.

```javascript
const element = <img src={user.avatarUrl} />;
```

JSX 태그는 자식을 포함할 수 있다.

```javascript
const element = (
  <div>
  	<h1>Hello!</h1>
  	<h2>Good to see you here.</h2>
  </div>
);
```

***

### Injection Attack 방지 <a href="#injection-attack" id="injection-attack"></a>

React DOM은 JSX에 들어있는 값을 렌더링하기 전에 `이스케이프 처리`를 한다. 따라서 애플리케이션에서 명시적으로 작성되지 않은 내용은 Injection 되지 않는다. 이런 특성으로 인해 `XSS(cross-site-scripting) 공격을 방지`할 수 있다.

이스케이프(escape)? 특정 문자를 원래의 기능에서 벗어나게 변환하는 행위

***

### JSX는 객체 모양 <a href="#jsx" id="jsx"></a>

Babel은 JSX를 `React.createElement()` 호출로 컴파일한다.

다음은 동일한 예시

```javascript
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```

```javascript
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

`React.createElement()`는 다음과 같은 객체를 생성한다.

```javascript
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
};
```

이러한 객체를 `React 엘리먼트`라고 하며, 나타나는 화면을 보여주는 코드라 생각하면 된다.

React는 이러한 객체를 읽고, 이를 사용하여 DOM을 구성하고 최신 상태로 유지한다.

JSX는 결국 React.createElement(component, props, ...children) 함수에 대한 `Syntax sugar`다.

즉, JSX는 React.createElement() 함수를 호출하기 위한 하나의 방법이며 JSX로 할 수 있는 모든 것은 순수 JS로도 할 수 있다.

> Reference\
> [https://reactjs.org/](https://reactjs.org/)
